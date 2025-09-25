# 🚀 ArgoCD GitOps Repository

This repository manages **GitOps-based deployments** for both **Connect Applications** and **Platform Engineering Tools**.  
We use **ArgoCD** as the GitOps engine and **Kustomize** for environment-specific overlays and configuration management.  

---

## ⚙️ How It Works

- **ArgoCD Applications** are defined as Kubernetes manifests in this repo.  
- **Kustomize** is used to:  
  - Compose base manifests  
  - Apply environment-specific overlays (e.g., `dev`, `stage`, `prod`)  
  - Ensure consistent, declarative configuration management  

---

## 📦 Connect Applications

- **connect-backend** → Backend services for the Connect platform.  
- **connect-frontend** → Frontend services for the Connect platform.  

---

## 🛠 Platform Engineering Tools

- **ArgoCD** → GitOps controller (self-managed).  
- **External Secrets** → Securely fetch secrets from external providers.  
- **Goldilocks** → Resource recommendations based on usage.  
- **Kubecost** → Cost monitoring and optimization.  
- **Kyverno** → Policy enforcement and validation.  
- **Linkerd** → Service mesh for secure, observable communication.  
- **Loki** → Centralized log collection and querying.  
- **Metrics Server** → Resource metrics for HPA. [Documentation](TOOLS/metrics-server/):
  - [Deployment Guide](TOOLS/metrics-server/DEPLOYMENT.md)
  - [Quick Start Guide](TOOLS/metrics-server/QUICKSTART.md)
  - [Troubleshooting](TOOLS/metrics-server/TROUBLESHOOTING.md)
  - [Examples & Use Cases](TOOLS/metrics-server/EXAMPLES.md)
- **Prometheus Stack** → Monitoring and alerting with Grafana dashboards.  
- **Redis Cluster** → In-memory data store with HA and sharding.  
- **Reloader** → Auto-reload workloads on secret/config changes.  
- **Traefik** → Ingress controller for routing traffic.  

---

## ✅ Deployment Flow

1. **Commit changes** to manifests in this repo.  
2. **ArgoCD** automatically syncs changes into the Kubernetes cluster.  
3. **Kustomize** ensures overlays and configs are applied consistently.  
4. **Cluster state** matches the desired state defined in Git.  

---

## 🔒 Security Considerations

- Sensitive values (e.g., credentials, tokens) managed via **External Secrets**.  
- Namespaces isolated per application/tool.  
- RBAC applied to restrict access.  
- Network policies recommended for inter-app communication.  
