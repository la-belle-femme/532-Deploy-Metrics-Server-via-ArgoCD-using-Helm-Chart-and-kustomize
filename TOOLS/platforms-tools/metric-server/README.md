# 📊 Metrics Server – Kubernetes Resource Metrics

Cluster-wide **resource usage metrics** for **autoscaling** and **observability**.

---

## 🚀 What is Metrics Server?

- Provides CPU/Memory metrics for HPA and VPA.  
- Enables `kubectl top nodes` and `kubectl top pods` commands.  
- Essential foundation for Kubernetes autoscaling and resource monitoring.  

---

## 🏗️ Repository Structure

```
metric-server/
├── base/                   # Common configuration
│   ├── application.yaml    # ArgoCD Application definition
│   ├── kustomization.yaml  # Base Kustomize config
│   └── values.yaml         # Shared Helm values (security, defaults)
├── overlays/               # Environment-specific overrides
│   ├── dev/                # Development
│   ├── sandbox/            # Staging/UAT
│   ├── prod/               # Production
│   └── release/            # Tag-based release environment
└── kustomization.yaml      # Root (points to one overlay by default)
```

---

## 🌍 Environments

| Environment | Branch       | Namespace            | Replicas | Deploy Type   | Resources    |
|-------------|--------------|----------------------|----------|---------------|--------------|
| Dev         | `develop`    | `kube-system`        | 1        | ✅ Auto-Deploy | Minimal      |
| Sandbox     | `sandbox`    | `kube-system`        | 2        | ✅ Auto-Deploy | Standard     |
| Production  | `production` | `kube-system`        | 3        | ⚠️ Manual     | High         |
| Release     | `tags/*`     | `kube-system`        | 5        | ⚠️ Manual     | Maximum      |

---

## 🎯 Workflow (Forgejo Branching Strategy)

```
Feature → Develop → Sandbox → Production → Release
   ↓         ↓         ↓          ↓          ↓
 Local     Auto       Auto      Manual     Manual
 Test     Deploy     Deploy     Deploy     Deploy
```

1. **Feature Branch**
   ```bash
   git checkout develop
   git pull origin develop
   git checkout -b feature/532-TeamWarriors-deploy-metric-server-via-argocd-using-helm-chart
   ```
2. **Make Changes & Test**
   ```bash
   kubectl apply -k overlays/dev/ --dry-run=client
   ```
3. **Commit & Push**
   ```bash
   git add .
   git commit -m "feat(platform-tools): add Metrics Server with base/overlay structure"
   git push origin feature/532-TeamWarriors-deploy-metric-server-via-argocd-using-helm-chart
   ```
4. **Open PR → Develop → Sandbox → Production**

---

## ⚙️ Configuration Dependencies

### 🔄 **Prometheus Stack Integration**

**IMPORTANT**: Before deploying metrics-server, verify where Prometheus Stack is deployed:

```bash
# Check existing Prometheus deployments
kubectl get deployments -A | grep prometheus
kubectl get namespaces | grep -E "(monitoring|observability|prometheus)"
```

**Update ServiceMonitor namespace** in the following files if needed:
- `base/application.yaml` → `serviceMonitor.namespace`  
- `base/values.yaml` → `serviceMonitor.namespace`

**Common namespace options:**
- `monitoring` (default assumption)
- `observability` (matches Jaeger pattern)  
- `prometheus-system` (alternative naming)

---

## ⚙️ Deployment Commands

- **Dev**
  ```bash
  kubectl apply -k overlays/dev/
  ```
- **Sandbox**
  ```bash
  kubectl apply -k overlays/sandbox/
  ```
- **Production**
  ```bash
  kubectl apply -k overlays/prod/
  ```
- **Release**
  ```bash
  kubectl apply -k overlays/release/
  ```

---

## 🔐 Security Features

- Non-root containers with dropped capabilities.  
- Resource requests/limits and PodDisruptionBudgets defined.  
- Infrastructure node placement for production environments.  
- Anti-affinity rules for high availability.  
- Priority classes for critical workload scheduling.  

---

## 🛠️ Validation & Troubleshooting

### Health Checks
```bash
kubectl get pods -n kube-system | grep metrics-server
kubectl top nodes
kubectl top pods --all-namespaces
argocd app get metrics-server-dev
```

### Common Issues
- **Metrics not available** → Check kubelet TLS configuration.  
- **Pod scheduling issues** → Verify node labels and tolerations.  
- **Resource constraints** → Review requests/limits per environment.  

---

## 📊 Platform Integration

- **HPA/VPA** → Provides metrics for horizontal/vertical pod autoscaling.  
- **Prometheus Stack** → Scrapes metrics-server endpoints for monitoring.  
- **Kyverno** → Enforces security policies on metrics-server deployments.  
- **Linkerd** → Service mesh integration for observability.  

---

## 🔄 ArgoCD Integration

- Check application list:
  ```bash
  argocd app list | grep metrics-server
  ```
- Get detailed info:
  ```bash
  argocd app get metrics-server-dev
  argocd app get metrics-server-sandbox
  argocd app get metrics-server-prod
  ```
- Sync application manually (prod/release):
  ```bash
  argocd app sync metrics-server-prod
  ```

---

## 📚 References

- [Metrics Server Official Docs](https://github.com/kubernetes-sigs/metrics-server)  
- [Kubernetes Resource Metrics](https://kubernetes.io/docs/tasks/debug/debug-cluster/resource-metrics-pipeline/)  
- Platform Team: `#platform-support` (Slack)  

---

**Ticket:** #532 • **Chart Version:** 3.12.1 • **Owner:** TeamWarriors  
**Last Updated:** Sept 24, 2025