# GitOps Continuous Delivery (`gitops`)

This repository contains the declarative GitOps definitions, Helm charts, and Kubernetes manifests managed continuously by **ArgoCD** on the AWS EKS cluster.

---

## Repository Layout

```
gitops/
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
└── manifests/                  # Plain Kubernetes manifests
    └── postgres/
```

---

## Production Reliability Standards

Every microservice Helm chart includes enterprise production Kubernetes configurations:

- **Resource Limits & Requests**: Configured with strict CPU and memory boundaries across all charts to prevent resource exhaustion on `t3.micro` nodes.
- **Horizontal Pod Autoscaling (HPA)**: Automatically scales pods based on CPU utilization (target 70%) and memory thresholds (target 80%).
- **Pod Disruption Budgets (PDB)**: Configured with `minAvailable: 1` to guarantee high availability during cluster node draining, upgrades, or spot instance reclaims.
- **Network Policies (`NetworkPolicy`)**: Enforces zero-trust egress and ingress isolation, allowing cross-service communication strictly on authorized TCP ports:
  - `courseapi`: TCP 8001 (allowed from `schedulerapi`)
  - `studentapi`: TCP 8002 (allowed from `schedulerapi`)
  - `ai-assistant-api`: TCP 8003 (allowed from `schedulerapi`)
  - `postgres`: TCP 5432 (allowed from internal microservices)

---

## Deployment with ArgoCD

Apply the single root application:

```bash
kubectl apply -f root-application.yaml
```

ArgoCD will automatically discover, reconcile, and sync all applications defined under `apps/`.
