# Metrics Server Deployment Documentation

## Architecture

### Component Overview
```
┌────────────────┐     ┌─────────────────┐     ┌──────────────┐
│   Kubernetes   │     │  Metrics Server  │     │    ArgoCD    │
│     Nodes      │────>│    (Metrics)    │<────│  Controller  │
└────────────────┘     └─────────────────┘     └──────────────┘
        │                      │                      │
        │                      │                      │
        v                      v                      v
┌────────────────┐     ┌─────────────────┐     ┌──────────────┐
│    Kubelet     │     │      HPA        │     │    Git       │
│  (Node Stats)  │     │  (Autoscaling)  │     │ Repository   │
└────────────────┘     └─────────────────┘     └──────────────┘
```

### Data Flow
1. Kubelet collects node and pod metrics
2. Metrics Server scrapes metrics from kubelets
3. HPA queries Metrics Server API
4. ArgoCD manages deployment from Git

### Key Components
- **Metrics Server**: Core metrics pipeline
- **ArgoCD**: Deployment automation
- **HPA**: Autoscaling controller
- **Kubelet**: Node-level metrics collection

## Overview and Implementation Details

This document details the successful deployment of Metrics Server via ArgoCD in our Kubernetes cluster. The deployment was completed on September 24, 2025.

### Current Status

✅ Deployment Status: Successfully deployed and operational
✅ Location: kube-system namespace
✅ Managed By: ArgoCD (platform-tools project)
✅ Health Status: Healthy
✅ Metrics Collection: Active and functional

### Verification Results

1. Pod Status:
```bash
metrics-server-6c69b9fbc4-hsqjv   1/1     Running   0   6m8s
```

2. API Service Status:
- Available: Yes
- Last Transition Time: 2025-09-24T23:34:49Z
- Status: All checks passed

3. Service Configuration:
- Type: ClusterIP
- Port: 443
- Target Port: https

### Implemented Features

1. Resource Monitoring:
   - CPU usage tracking
   - Memory usage tracking
   - Node metrics collection
   - Pod metrics collection

2. Auto-scaling Support:
   - HPA integration
   - Custom thresholds configured
   - Scaling policies implemented

### Configuration Details

1. Kustomize Configuration:
```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
namespace: kube-system
resources:
- metrics-server.yaml
```

2. ArgoCD Integration:
- Project: platform-tools
- Sync Policy: Automated
- Prune Policy: Enabled
- Self-Heal: Enabled

### Testing Results

1. Metrics Collection:
- Node Metrics: ✅ Working
- Pod Metrics: ✅ Working
- API Endpoints: ✅ Available

2. HPA Integration:
- Auto-scaling: ✅ Functional
- Custom Metrics: ✅ Available
- Threshold Monitoring: ✅ Active

### Maintenance Procedures

1. Updating Configuration:
   - Make changes in the Git repository
   - ArgoCD will automatically sync changes
   - Monitor deployment status in ArgoCD UI

2. Troubleshooting:
   - Check pod logs: `kubectl logs -n kube-system -l k8s-app=metrics-server`
   - Verify API service: `kubectl get apiservice v1beta1.metrics.k8s.io`
   - Monitor metrics: `kubectl top nodes` and `kubectl top pods`

### Security Configuration

1. Current Settings:
   - TLS: Insecure TLS enabled (development setting)
   - RBAC: Configured with minimal permissions
   - Service Account: Dedicated metrics-server account

2. Access Controls:
   - Namespace restricted
   - RBAC policies applied
   - API service properly configured

### Next Steps and Recommendations

1. Monitoring:
   - Set up alerts for metrics-server availability
   - Monitor resource usage regularly
   - Review scaling thresholds periodically

2. Security:
   - Review TLS configuration for production
   - Audit RBAC permissions
   - Consider implementing network policies

3. Documentation:
   - Keep deployment documentation updated
   - Document any configuration changes
   - Maintain troubleshooting guides

### Support Information

For issues or assistance:
1. Check ArgoCD UI for sync status
2. Review pod logs for errors
3. Contact DevOps team if needed

### Useful Commands

```bash
# Check pod status
kubectl get pods -n kube-system -l k8s-app=metrics-server

# View metrics
kubectl top nodes
kubectl top pods -A

# Check logs
kubectl logs -n kube-system -l k8s-app=metrics-server

# Verify API service
kubectl get apiservice v1beta1.metrics.k8s.io
```