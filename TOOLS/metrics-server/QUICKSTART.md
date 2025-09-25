# Quick Start Guide: Metrics Server

## 🚀 Getting Started

This guide will help you quickly understand and work with our Metrics Server deployment in Kubernetes using ArgoCD.

### Prerequisites Checklist
- [ ] Access to Kubernetes cluster
- [ ] `kubectl` installed and configured
- [ ] ArgoCD UI access
- [ ] Git repository access

## 🔑 Initial Setup

### 1. Access the Environment

```bash
# Verify cluster access
kubectl cluster-info

# Check ArgoCD access
kubectl get pods -n argocd
```

### 2. Quick Access to Metrics Server

```bash
# Check metrics-server status
kubectl get pods -n kube-system -l k8s-app=metrics-server

# View basic metrics
kubectl top nodes
kubectl top pods -A
```

## 📊 Common Tasks

### View Resource Usage

```bash
# Check node resources
kubectl top nodes

# Check pod resources in specific namespace
kubectl top pods -n your-namespace

# Get detailed pod metrics
kubectl top pods -n kube-system --containers=true
```

### Monitor Specific Workload

```bash
# Watch pod metrics in real-time
watch kubectl top pods -n your-namespace

# Get specific pod metrics
kubectl top pod <pod-name> -n <namespace>
```

## 🔄 Working with HPA

### Quick HPA Setup

1. Create basic HPA:
```bash
kubectl autoscale deployment <deployment-name> \
  --cpu-percent=50 \
  --min=1 \
  --max=10 \
  -n <namespace>
```

2. Check HPA status:
```bash
kubectl get hpa -n <namespace>
```

## 🔍 Quick Troubleshooting

### Common Issues & Quick Fixes

1. Metrics Not Available
```bash
# Restart metrics-server
kubectl rollout restart deployment metrics-server -n kube-system
```

2. Check Logs
```bash
# View metrics-server logs
kubectl logs -n kube-system -l k8s-app=metrics-server
```

## 🛠️ Daily Operations

### Health Check Commands

```bash
# 1. Check metrics-server health
kubectl get apiservice v1beta1.metrics.k8s.io

# 2. Verify metrics collection
kubectl top nodes

# 3. Check ArgoCD sync status
kubectl get application metrics-server -n argocd
```

### Common Operations

1. View Resource Usage:
```bash
kubectl top pods -A --sort-by=cpu
kubectl top pods -A --sort-by=memory
```

2. Monitor Specific Namespace:
```bash
kubectl top pods -n your-namespace --containers
```

## 📝 Quick Reference

### Important Endpoints

- ArgoCD UI: https://argocd.your-domain.com
- Metrics API: https://kubernetes.default.svc/apis/metrics.k8s.io/v1beta1

### Key Files Location

```
TOOLS/
└── metrics-server/
    ├── kustomization.yaml      # Main configuration
    ├── metrics-server.yaml     # Core deployment
    └── DEPLOYMENT.md          # Full documentation
```

### Essential Labels

- Application: `k8s-app=metrics-server`
- Namespace: `kube-system`
- Project: `platform-tools`

## 🎯 Quick Validation Steps

1. Check Pod Status:
```bash
kubectl get pods -n kube-system -l k8s-app=metrics-server
# Should show: Running 1/1
```

2. Verify Metrics:
```bash
kubectl top nodes
# Should show: CPU and Memory metrics
```

3. Check API:
```bash
kubectl api-resources | grep metrics
# Should show: metrics.k8s.io endpoints
```

## ⚡ Quick Tips

1. Use watch for real-time monitoring:
```bash
watch -n 5 kubectl top nodes
```

2. Sort pods by resource usage:
```bash
kubectl top pods -A --sort-by=cpu
```

3. Get detailed metrics:
```bash
kubectl top pods --containers=true -A
```

## 🆘 Need Help?

1. Check our documentation:
   - `DEPLOYMENT.md` - Full deployment details
   - `TROUBLESHOOTING.md` - Common issues and solutions
   - `EXAMPLES.md` - Usage examples and patterns

2. Quick Support:
   - Slack: #metrics-server-support
   - Email: devops-team@your-company.com
   - ArgoCD UI: Check application status

## 🔄 Daily Workflow

1. Morning Check:
   ```bash
   kubectl top nodes  # Check cluster health
   kubectl get pods -n kube-system -l k8s-app=metrics-server  # Check metrics-server
   kubectl get hpa -A  # Check autoscaling status
   ```

2. Issue Detection:
   ```bash
   kubectl logs -n kube-system -l k8s-app=metrics-server --tail=100  # Check recent logs
   kubectl describe pod -n kube-system -l k8s-app=metrics-server  # Check pod events
   ```

Remember: Always check the ArgoCD UI for sync status when making changes!

## 🔐 Security Best Practices

1. Access Control:
   ```bash
   # Check RBAC permissions
   kubectl auth can-i get pods -n kube-system
   kubectl auth can-i get metrics.metrics.k8s.io
   ```

2. Security Context:
   ```bash
   # Verify security context
   kubectl get pod -n kube-system -l k8s-app=metrics-server -o jsonpath='{.spec.securityContext}'
   ```

3. TLS Configuration:
   ```bash
   # Check TLS settings
   kubectl get deployment metrics-server -n kube-system -o jsonpath='{.spec.template.spec.containers[0].args}'
   ```

## 📈 Performance Monitoring

1. Basic Health Metrics:
   ```bash
   # Check metrics-server resource usage
   kubectl top pod -n kube-system -l k8s-app=metrics-server
   
   # Check response latency
   kubectl get --raw /metrics | grep apiserver_request_duration_seconds
   ```

2. Capacity Planning:
   ```bash
   # View all resource requests/limits
   kubectl describe pod -n kube-system -l k8s-app=metrics-server | grep -A 3 Requests
   ```

## 🔄 Version Management

1. Check Current Version:
   ```bash
   # Get image version
   kubectl get deployment metrics-server -n kube-system -o jsonpath='{.spec.template.spec.containers[0].image}'
   ```

2. Update Process:
   ```bash
   # View update history
   kubectl rollout history deployment metrics-server -n kube-system
   
   # Check rollout status
   kubectl rollout status deployment metrics-server -n kube-system
   ```

## 🎯 Quick Validation Tests

1. Metrics Availability Test:
   ```bash
   # Create test pod
   kubectl run test-metrics --image=busybox --restart=Never -- /bin/sh -c "sleep 3600"
   
   # Wait 1 minute, then check metrics
   kubectl top pod test-metrics
   
   # Clean up
   kubectl delete pod test-metrics
   ```

2. HPA Test:
   ```bash
   # Deploy test application
   kubectl create deployment php-apache --image=k8s.gcr.io/hpa-example
   kubectl expose deployment php-apache --port=80
   
   # Create HPA
   kubectl autoscale deployment php-apache --cpu-percent=50 --min=1 --max=5
   
   # Generate load
   kubectl run -i --tty load-generator --rm --image=busybox --restart=Never -- /bin/sh -c "while sleep 0.01; do wget -q -O- http://php-apache; done"
   ```

## 🌐 Multi-Environment Tips

1. Development Environment:
   ```bash
   # Lower resource settings
   kubectl patch deployment metrics-server -n kube-system --patch '{"spec":{"template":{"spec":{"containers":[{"name":"metrics-server","resources":{"requests":{"cpu":"50m","memory":"75Mi"}}}]}}}}'
   ```

2. Production Environment:
   ```bash
   # Enable detailed logging
   kubectl patch deployment metrics-server -n kube-system --patch '{"spec":{"template":{"spec":{"containers":[{"name":"metrics-server","args":["--v=2"]}]}}}}'
   ```

## 📊 Metrics Dashboard

Quick commands to view metrics in different formats:

1. Table Format:
   ```bash
   # Node metrics
   kubectl top nodes | column -t
   
   # Pod metrics
   kubectl top pods -A | column -t
   ```

2. Sort by Resource Usage:
   ```bash
   # Sort by CPU
   kubectl top pods -A --sort-by=cpu
   
   # Sort by memory
   kubectl top pods -A --sort-by=memory
   ```

## 🚨 Alert Monitoring

1. Check for Issues:
   ```bash
   # View events
   kubectl get events -n kube-system | grep metrics-server
   
   # Check pod status changes
   kubectl get pods -n kube-system -l k8s-app=metrics-server -w
   ```

2. Set Up Monitoring:
   ```bash
   # Watch resource usage
   watch -n 10 "kubectl top nodes && echo '' && kubectl top pods -n kube-system | grep metrics-server"
   ```

Remember: These additional commands and configurations should be used alongside the core functionality covered earlier in this guide.