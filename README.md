# Welcome to my Home(lab) 🏡

<https://www.pedrotchang.dev/>

## Introduction
I am a big proponent to giving credit where it is due, and I would first like to take the time to thank Mischa van den Burg
and the Kubecraft community. I could not learn to do all this without them!

If you have ever been curious about Cloud Native Technologies, DevOps or Kubernetes, then the place you want to be is in Kubecraft!

This repository is where I do all my testing, tinkering, and all-in-all a space for me to play, and work! Here you will find all tthe documentation of my homelab.

It does have a serious note as well, since I will be working with my own personal data and require me to think about the whole process of deployment and maintanence!

## Hardware & Cluster Provisioning

I like to start off talking about hardware first. It's I think a great base line to understand what I was working with.

I have currently 3 old hardware:\
HP EliteDesk 800 G2 i5-6500T/16GB/256SSD x 2\
HP Laptop (atm do not know the model) 8GB/256SSD

I love [Talos Linux](https://www.talos.dev/). It is secure, lightweight, and has robust features. At first, I used straight baremetal. But after sometime (a week...), I realized that [Omni](https://www.siderolabs.com/platform/saas-for-kubernetes/) was the way to go.

I could spin up new clusters in seconds, and exposing external services is a breeze.
I have them in this structure:

| Cluster | Usage | Hardware |
| --------------- | --------------- | --------------- |
| Data | PostgreSQL Database | HP Laptop |
| Tachtit | Apps | HP 800 G2 |
| ~~Redacted~~ | Private Apps | HP 800 G2 |

## Apps

### Infrastructure Applications 🚧 ( Some Apps are Under Construction)

| Icon | Name | Description |
|------|------|-------------|
| <img src="https://cdn.jsdelivr.net/gh/homarr-labs/dashboard-icons/png/cilium.png" width="30" alt="Cilium logo"> | Cilium | An amazing CNI used for all my clusters. I opt out of Flannel for Cilium. |
| <img src="https://avatars.githubusercontent.com/u/100373852?s=200&v=4" width="30" alt="CloudnativePG logo"> | CloudnativePG | A Kubernetes operator for deploying and managing PostgreSQL clusters. |
| <img src="https://raw.githubusercontent.com/external-secrets/external-secrets/refs/heads/main/assets/eso-logo-large.png" width="30" alt="External Secrets logo"> | External Secrets Operator | A Kubernetes operator that synchronizes secrets from external APIs into Kubernetes. Currently, it uses secrets from my Azure Key Vault. |
| <img src="https://raw.githubusercontent.com/kubernetes-sigs/external-dns/refs/heads/master/docs/img/external-dns.png" width="30" alt="External DNS logo"> | External DNS | A Kubernetes addon that automates the management of DNS records based on Kubernetes resources. |
| <img src="https://cdn.jsdelivr.net/gh/homarr-labs/dashboard-icons/png/flux-cd.png" width="30" alt="FluxCD logo"> | FluxCD | A GitOps tool for automating Kubernetes deployments from Git repositories. |
| <img src="https://cdn.jsdelivr.net/gh/homarr-labs/dashboard-icons/png/grafana.png" width="30" alt="Grafana logo"> | Grafana 🚧 | A multi-platform analytics and visualization web application for monitoring data. |
| <img src="https://cdn.jsdelivr.net/gh/homarr-labs/dashboard-icons/png/prometheus.png" width="30" alt="Prometheus logo"> | Prometheus 🚧 | An open-source monitoring and alerting toolkit for containers and microservices. |
| <img src="https://avatars.githubusercontent.com/ml/287?s=82&v=4" width="30" alt="Renovate logo"> | Renovate | An automated dependency update tool that creates and maintains pull requests for your dependencies. |

### End User Applications

| Icon | Name | Description |
|------|------|-------------|
| <img src="https://cdn.jsdelivr.net/gh/homarr-labs/dashboard-icons/png/linkding.png" width="30" alt="Linkding logo"> | Linkding | A self-hosted bookmark manager with tagging and search functionality. |

## Next Steps

Or in other words, a TODO list:
- [ ] Link my Po

