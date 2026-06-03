# Kubernetes Microservices

Production-grade Kubernetes configuration for deploying a microservices application on AWS EKS. Includes raw manifests, a Helm chart, Horizontal Pod Autoscaler, and Ingress with TLS.

## Structure

```
.
├── k8s/
│   ├── namespace.yaml        # Isolated namespace with resource quotas
│   ├── configmap.yaml        # App configuration
│   ├── deployment.yaml       # App deployment with rolling updates
│   ├── service.yaml          # ClusterIP + LoadBalancer services
│   ├── ingress.yaml          # NGINX ingress with TLS
│   └── hpa.yaml              # Horizontal Pod Autoscaler
├── helm/
│   ├── Chart.yaml
│   ├── values.yaml           # Default values (overridden per environment)
│   ├── values-dev.yaml
│   ├── values-prod.yaml
│   └── templates/
│       ├── deployment.yaml
│       ├── service.yaml
│       ├── ingress.yaml
│       ├── hpa.yaml
│       └── configmap.yaml
└── Dockerfile
```

## Prerequisites

- kubectl >= 1.28
- Helm >= 3.12
- AWS EKS cluster (`eksctl` or Terraform)
- NGINX Ingress Controller installed

## Deploy with kubectl

```bash
kubectl apply -f k8s/namespace.yaml
kubectl apply -f k8s/configmap.yaml
kubectl apply -f k8s/deployment.yaml
kubectl apply -f k8s/service.yaml
kubectl apply -f k8s/ingress.yaml
kubectl apply -f k8s/hpa.yaml
```

## Deploy with Helm

```bash
# Dev
helm upgrade --install myapp ./helm \
  -f helm/values-dev.yaml \
  --namespace myapp-dev --create-namespace

# Prod
helm upgrade --install myapp ./helm \
  -f helm/values-prod.yaml \
  --namespace myapp-prod --create-namespace
```

## Key Features

- **Rolling updates** with zero-downtime deploys (maxSurge=1, maxUnavailable=0)
- **HPA** scales on CPU (target 70%) and memory (target 80%) — 2 to 10 replicas
- **Liveness / readiness probes** on `/health` and `/ready`
- **Resource requests/limits** enforced at both pod and namespace level
- **TLS ingress** via cert-manager ClusterIssuer