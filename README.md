# Welcome to my Home(lab) 🏡

<https://www.pedrotchang.dev/>

## Introduction

Here you will find all tthe documentation of my homelab.

This repository is where I do all my testing, tinkering, and all-in-all a space for me to play, and work! It does have a serious note as well, since I will be working with my own personal data and require me to think about the whole process of deployment and maintanence!

## Hardware & Cluster Provisioning

I like to start off talking about hardware first. It's I think a great base line to understand what I was working with.

I have currently 3 old hardware:\
HP EliteDesk 800 G2 i5-6500T/16GB/256SSD\
HP EliteDesk 800 G2 i5-6500T/16GB/256SSD\
HP Laptop (atm do not know the model) 8GB/256SSD

I love [Talos Linux](https://www.talos.dev/). It has production grade security out of the box, is lightweight, and has robust features. At first, I used straight baremetal. But after sometime (a week...), I realized that [Omni](https://www.siderolabs.com/platform/saas-for-kubernetes/) was the way to go.

I could spin up new clusters in seconds, and exposing external services is a breeze.

I have them in this structure:

| Cluster | Usage | Hardware |
| --------------- | --------------- | --------------- |
| Data | PostgreSQL Database | HP Laptop |
| Tachtit | Apps | HP 800 G2 x 2 |

## Apps

| Icon | Name | Description |
|------|------|-------------|
| <img src="https://cdn.jsdelivr.net/gh/homarr-labs/dashboard-icons/png/cilium.png" width="30" alt="Cilium logo"> | [Cilium](https://cilium.io/) | An amazing CNI used for all my clusters. I opt out of Flannel for Cilium. |
| <img src="https://avatars.githubusercontent.com/u/100373852?s=200&v=4" width="30" alt="CloudnativePG logo"> | [CloudnativePG](https://cloudnative-pg.io/) | A Kubernetes operator for deploying and managing PostgreSQL clusters. |
| <img src="https://raw.githubusercontent.com/external-secrets/external-secrets/refs/heads/main/assets/eso-logo-large.png" width="30" alt="External Secrets logo"> | [External Secrets Operator](https://external-secrets.io/) | A Kubernetes operator that synchronizes secrets from external APIs into Kubernetes. Currently, it uses secrets from my Azure Key Vault. |
| <img src="https://raw.githubusercontent.com/kubernetes-sigs/external-dns/refs/heads/master/docs/img/external-dns.png" width="30" alt="External DNS logo"> | [External DNS](https://github.com/kubernetes-sigs/external-dns) | A Kubernetes addon that automates the management of DNS records based on Kubernetes resources. |
| <img src="https://cdn.jsdelivr.net/gh/homarr-labs/dashboard-icons/png/flux-cd.png" width="30" alt="FluxCD logo"> | [FluxCD](https://fluxcd.io/) | A GitOps tool for automating Kubernetes deployments from Git repositories. |
| <img src="https://avatars.githubusercontent.com/u/158298505?s=48&v=4" width="30" alt="Flux Operator logo"> | [Flux Operator](https://github.com/flux-framework/flux-operator) | A Kubernetes CRD controller that manages the lifecycle of CNCF Flux. It automates installation, configuration, and upgrades of Flux controllers through a declarative API. |
| <img src="https://cdn.jsdelivr.net/gh/homarr-labs/dashboard-icons/png/grafana.png" width="30" alt="Grafana logo"> | [Grafana](https://grafana.com/) | A multi-platform analytics and visualization web application for monitoring data. |
| <img src="https://cdn.jsdelivr.net/gh/homarr-labs/dashboard-icons/png/prometheus.png" width="30" alt="Prometheus logo"> | [Prometheus](https://prometheus.io/) | An open-source monitoring and alerting toolkit for containers and microservices. |
| <img src="https://avatars.githubusercontent.com/ml/287?s=82&v=4" width="30" alt="Renovate logo"> | [Renovate](https://www.mend.io/renovate/) | An automated dependency update tool that creates and maintains pull requests for your dependencies. |

### End User Applications

| Icon | Name | Description |
|------|------|-------------|
| <img src="https://avatars.githubusercontent.com/u/122929872?s=48&v=4" width="30" alt="Homepage logo"> | [Homepage](https://gethomepage.dev/) | A self-hosted dashboard that allows you to organize your web services and bookmarks in a clean, customizable interface. |
| <img src="https://cdn.jsdelivr.net/gh/homarr-labs/dashboard-icons/png/linkding.png" width="30" alt="Linkding logo"> | [Linkding](https://github.com/sissbruecker/linkding) | A self-hosted bookmark manager with tagging and search functionality. |
| <img src="https://cdn.jsdelivr.net/gh/homarr-labs/dashboard-icons/png/wallabag.png" width="30" alt="Wallabag logo"> | [Wallabag](https://wallabag.org/) | A self-hosted read-it-later application that saves web articles and strips away clutter for comfortable reading. |
| <img src="https://cdn.jsdelivr.net/gh/homarr-labs/dashboard-icons/png/n8n.png" width="30" alt="n8n logo"> | [n8n](https://n8n.io/) | A self-hosted workflow automation tool that connects apps and services with a visual node-based editor. |
| <img src="https://cdn.jsdelivr.net/gh/homarr-labs/dashboard-icons/png/commafeed.png" width="30" alt="CommaFeed logo"> | [CommaFeed](https://www.commafeed.com/) | A self-hosted RSS reader inspired by Google Reader with a clean, responsive interface. |

## Next Steps

- [ ] Link my PostgreSQL Database in the data cluster to my TrueNAS Scale.
- [x] Have blob storage for my data cluster to create periodic backups.
- [x] Add on ~~Immich~~, CommaFeed, Wallabag, to work with my data cluster, and also backup to blob storage.

