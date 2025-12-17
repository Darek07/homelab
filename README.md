# Homelab Kubernetes (k3s + Flux)

This repository contains my homelab Kubernetes configuration managed with **GitOps** using **Flux CD** and **Kustomize**, running on **k3s**.

## Overview

- **k3s** as the Kubernetes distribution
- **Flux CD** for continuous reconciliation from Git
- **Kustomize** for environment overlays
- Environment-focused layout (`base` / `dev`)
- Apps, infrastructure, and monitoring managed declaratively

## Repository Structure

- `clusters/` – Flux bootstrap and cluster-level kustomizations
- `apps/` – Application manifests with base and environment overlays
- `infrastructure/` – Shared controllers and cluster configuration
- `monitoring/` – Observability stack and related configs
- `renovate.json` – Automated dependency updates

## Environments

Currently defined:
- `dev`

Each environment uses Kustomize overlays to customize base resources.

## GitOps Flow

1. Flux is bootstrapped from `clusters/<env>`
2. Cluster kustomizations reference apps, infrastructure, and monitoring
3. Changes are applied automatically when committed to Git

## Hardware

Currently running on a single-node k3s cluster:

- **Node**: 15-year-old laptop
- **CPU**: Intel(R) Pentium(R) B950 @ 2.10GHz
- **Memory**: 4 GB RAM

## Security & Access

- **Secrets** are encrypted using **SOPS** with [age](https://github.com/FiloSottile/age) and stored safely in Git.
- **Public access** to selected apps is provided via **Cloudflare Tunnel**.
  - Domain details are intentionally not shared to avoid unnecessary exposure and traffic.

## Notes

- This repo reflects personal homelab usage and experimentation
