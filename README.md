# GitOps Repository

This repository contains the GitOps definitions (Helm charts and Kubernetes manifests) managed continuously by **ArgoCD** on EKS.

## Repository Layout

```
.
├── root-application.yaml       # ArgoCD "App of Apps" root definition
├── apps/                       # Individual ArgoCD Application CRDs
│   ├── postgres-app.yaml
│   ├── courseapi-app.yaml
│   ├── studentapi-app.yaml
│   ├── schedulerapi-app.yaml
│   └── ai-assistant-api-app.yaml
├── charts/                     # Helm Charts for each microservice
│   ├── courseapi/
│   ├── studentapi/
│   ├── schedulerapi/
│   └── ai-assistant-api/
└── manifests/                  # Plain Kubernetes manifests (PostgreSQL)
    └── postgres/
```

## How to Deploy Everything with ArgoCD

You only need to apply the single root application:

```bash
kubectl apply -f root-application.yaml
```

ArgoCD will automatically discover and sync all applications defined under `apps/`.
