🔗 **Central Documentation:** [https://github.com/fitforge101/fitforge-app-docs](https://github.com/fitforge101/fitforge-app-docs)

# Helm Charts & Deployment

## Overview
The `fitforge-charts` repository manages the GitOps deployment strategy for the entire platform. It utilizes Helm charts managed by ArgoCD to ensure declarative, reproducible Kubernetes environments.

## Deployment Strategy
FitForge uses the **ArgoCD "App of Apps" pattern**. The root application (`fitforge-root.yaml`) points to this repository, cascading deployments for infrastructure (databases, Redis) and all organizational microservices.

## Chart Structure
```text
.
├── argocd-apps/
│   └── fitforge-root.yaml      # The root ArgoCD application
├── infrastructure/             # Dependencies (MongoDB, Redis, Envoy)
└── microservices/              # Application charts
    ├── ai-agent-service/
    ├── auth-service/
    ├── nutrition-service/
    ├── progress-service/
    ├── user-service/
    └── workout-service/
```

## `values.yaml` Usage
Each microservice chart contains a `values.yaml` file controlling environment-specific configurations:
*   **Image Tags**: Updated automatically by CI pipelines post-build.
*   **Replica Counts**: Controls horizontal scaling manually.
*   **Resource Limits**: Sets CPU and Memory constraints (`requests`/`limits`).
*   **Autoscaling**: Toggles Horizontal Pod Autoscaler (HPA) settings.

## How Services are Deployed in Kubernetes
1.  CI workflows build and push new Docker images.
2.  The workflow commits the new image tag to the corresponding `values.yaml` in this repository.
3.  ArgoCD detects the Git commit and reconciles the cluster state, deploying the new pods seamlessly via rolling updates.

## Setup Instructions
1.  Ensure ArgoCD is running in your Kubernetes cluster.
2.  Apply the root application:
    ```bash
    kubectl apply -f argocd-apps/fitforge-root.yaml
    ```
