# Kagent Adapter Setup Instructions

This document provides step-by-step instructions for deploying the OpenAI→A2A adapter and Open WebUI for Kagent.

## Overview

The implementation consists of:
1. **kagent-adapter** - Go service that translates OpenAI API requests to Kagent's A2A protocol
2. **Open WebUI** - Modern chat interface
3. **Cloudflare Tunnel** - External access to Open WebUI

## Prerequisites

- GitHub account with access to `github.com/pedrotchang` org
- Azure Key Vault access (for Cloudflare tunnel credentials)
- Cloudflare account with existing tunnel or ability to create new tunnel

## Step 1: Create kagent-adapter Repository

The adapter code has been prepared in `/tmp/kagent-adapter-repo/`. You need to create a new GitHub repository and push this code.

```bash
# Create new repo on GitHub: github.com/pedrotchang/kagent-adapter
# Then push the code:

cd /tmp/kagent-adapter-repo

# Initialize git
git init
git add .
git commit -m "feat: initial kagent-adapter implementation"

# Add remote and push
git remote add origin git@github.com:pedrotchang/kagent-adapter.git
git branch -M main
git push -u origin main
```

The GitHub Actions workflow will automatically:
- Build the Docker image on push to main
- Push to `ghcr.io/pedrotchang/kagent-adapter:latest`
- Tag with commit SHA for versioning

## Step 2: Set up Cloudflare Tunnel

You need to create a Cloudflare tunnel for `kagent.seyzahl.com` or use an existing tunnel.

### Option A: Create New Tunnel

```bash
# Install cloudflared if not already installed
# brew install cloudflare/cloudflare/cloudflared  # macOS
# Or download from https://github.com/cloudflare/cloudflared/releases

# Login to Cloudflare
cloudflared tunnel login

# Create tunnel
cloudflared tunnel create kagent

# This creates a credentials file. Note its location.
# Example: ~/.cloudflared/<TUNNEL-ID>.json
```

### Option B: Use Existing Tunnel

If you already have a tunnel that can route `kagent.seyzahl.com`, you can use that instead. Just update the ConfigMap in `apps/tachtit/kagent/cloudflare.yaml` to use your tunnel name.

## Step 3: Add Tunnel Credentials to Azure Key Vault

The tunnel credentials JSON needs to be stored in Azure Key Vault.

```bash
# Read your tunnel credentials file
TUNNEL_CREDS=$(cat ~/.cloudflared/<TUNNEL-ID>.json)

# Add to Azure Key Vault (adjust vault name as needed)
az keyvault secret set \
  --vault-name <your-vault-name> \
  --name kagent-cloudflare-tunnel-credentials \
  --value "$TUNNEL_CREDS"
```

The ExternalSecret in `apps/tachtit/kagent/cloudflare.yaml` will automatically sync this to Kubernetes.

## Step 4: Configure Cloudflare DNS

Add a CNAME record for `kagent.seyzahl.com`:

```
Type: CNAME
Name: kagent
Target: <TUNNEL-ID>.cfargotunnel.com
Proxy: Yes (orange cloud)
```

## Step 5: Deploy to Kubernetes

Once the Docker image is built and tunnel credentials are set:

```bash
# Commit homelab changes
cd /homelab
git add apps/base/kagent-adapter apps/tachtit/kagent apps/base/homepage README.md
git commit -m "feat: add kagent adapter and Open WebUI integration"
git push

# FluxCD will automatically reconcile and deploy:
# 1. kagent-adapter deployment
# 2. Open WebUI via Helm
# 3. Cloudflare tunnel

# Watch the deployment
kubectl get pods -n kagent -w
```

## Step 6: Verify Deployment

```bash
# Check all components are running
kubectl get pods -n kagent

# Expected pods:
# - kagent-adapter-<hash>-<hash> (2 replicas)
# - open-webui-<hash>-<hash>
# - cloudflared-<hash>-<hash> (2 replicas)
# - kagent-ui-<hash>-<hash> (still running, for debugging)
# - [existing kagent pods]

# Check adapter logs
kubectl logs -n kagent deployment/kagent-adapter

# Check Open WebUI logs
kubectl logs -n kagent deployment/open-webui

# Test adapter endpoint (port-forward)
kubectl port-forward -n kagent svc/kagent-adapter 8080:8080

# In another terminal:
curl http://localhost:8080/v1/models
# Should return list of kagent agents as "models"
```

## Step 7: Access Open WebUI

Open https://kagent.seyzahl.com in your browser. You should see Open WebUI interface.

**First-time setup**:
1. Open WebUI will ask you to create an account (local only, no external auth)
2. Once logged in, you should see Kagent agents in the model dropdown (e.g., "kagent/k8s-agent")
3. Start chatting!

## Step 8: Test Agent Functionality

Try a simple test with the k8s-agent:

```
User: List all pods in the kagent namespace
Agent: [Should use kubectl tools to list pods]
```

Verify that:
- ✅ Streaming responses work
- ✅ MCP tools are accessible (kubectl, helm, etc.)
- ✅ All agents appear in model selector

## Troubleshooting

### Adapter not starting

```bash
kubectl describe pod -n kagent <kagent-adapter-pod>

# Check RBAC permissions
kubectl auth can-i list agents.kagent.dev --as=system:serviceaccount:kagent:kagent-adapter -n kagent
```

### Open WebUI can't connect to adapter

```bash
# Check service exists
kubectl get svc -n kagent kagent-adapter

# Test adapter from inside cluster
kubectl run -it --rm debug --image=curlimages/curl --restart=Never -- \
  curl http://kagent-adapter.kagent.svc.cluster.local:8080/v1/models
```

### Cloudflare tunnel not working

```bash
# Check tunnel pods
kubectl logs -n kagent deployment/cloudflared

# Verify DNS
dig kagent.seyzahl.com

# Check external secret
kubectl get externalsecret -n kagent cloudflared-tunnel
kubectl describe externalsecret -n kagent cloudflared-tunnel
```

### Agents not appearing in Open WebUI

```bash
# Check adapter can list agents
kubectl port-forward -n kagent svc/kagent-adapter 8080:8080
curl http://localhost:8080/v1/models

# Check Open WebUI environment variables
kubectl get deployment -n kagent open-webui -o yaml | grep -A 10 "env:"
```

## Rollback

If you need to rollback:

```bash
# Revert homelab changes
git revert HEAD
git push

# FluxCD will automatically remove:
# - kagent-adapter deployment
# - Open WebUI
# - Cloudflare tunnel

# Access kagent-ui via port-forward
kubectl port-forward -n kagent svc/kagent-ui 8080:8080
# Open http://localhost:8080
```

## Architecture Diagram

```
┌─────────────┐
│   Browser   │
└──────┬──────┘
       │ HTTPS
       ▼
┌─────────────────────┐
│ Cloudflare Tunnel   │ kagent.seyzahl.com
└──────┬──────────────┘
       │
       ▼
┌─────────────────────┐
│    Open WebUI       │ Port 8080
└──────┬──────────────┘
       │ HTTP (OpenAI API)
       ▼
┌─────────────────────┐
│  kagent-adapter     │ Port 8080
└──────┬──────────────┘
       │ A2A Protocol
       ▼
┌─────────────────────┐
│ kagent-controller   │ Port 8083
└──────┬──────────────┘
       │
       ▼
┌─────────────────────┐
│   Kagent Agents     │ (k8s-agent, helm-agent, etc.)
└──────┬──────────────┘
       │
       ▼
┌─────────────────────┐
│    MCP Servers      │ (kubectl, helm, n8n, etc.)
└─────────────────────┘
```

## Files Created

### Homelab Repository
- `apps/base/kagent-adapter/deployment.yaml` - Adapter deployment
- `apps/base/kagent-adapter/service.yaml` - Adapter service
- `apps/base/kagent-adapter/serviceaccount.yaml` - Service account
- `apps/base/kagent-adapter/rbac.yaml` - RBAC for agent access
- `apps/base/kagent-adapter/open-webui-repository.yaml` - Helm repository
- `apps/base/kagent-adapter/open-webui-release.yaml` - Open WebUI Helm release
- `apps/base/kagent-adapter/kustomization.yaml` - Kustomization
- `apps/tachtit/kagent/cloudflare.yaml` - Cloudflare tunnel config
- `apps/tachtit/kagent/kustomization.yaml` - Updated to include adapter

### Separate Repository (kagent-adapter)
- `main.go` - Go adapter implementation (~300 lines)
- `go.mod` - Go dependencies
- `Dockerfile` - Multi-stage Docker build
- `.github/workflows/docker-build.yml` - CI/CD pipeline
- `README.md` - Adapter documentation

## Support

- Kagent: https://kagent.dev/docs
- Open WebUI: https://docs.openwebui.com
- Issues: https://github.com/pedrotchang/kagent-adapter/issues
