# Metrics Server Troubleshooting Guide

## Common Issues and Solutions

### 1. Metrics Not Available

**Symptom:**
```bash
kubectl top nodes
Error from server (ServiceUnavailable): the server is currently unable to handle the request
```

**Solutions:**
1. Check metrics-server pod status:
```bash
kubectl get pods -n kube-system -l k8s-app=metrics-server
```

2. Verify logs for errors:
```bash
kubectl logs -n kube-system -l k8s-app=metrics-server
```

3. Common fixes:
- Wait 1-2 minutes for metrics collection to start
- Check if kubelet certificates are properly configured
- Verify network connectivity between metrics-server and nodes

### 2. Pod Startup Issues

**Symptom:** Pod stays in Pending/CrashLoopBackOff state

**Solutions:**
1. Check pod events:
```bash
kubectl describe pod -n kube-system -l k8s-app=metrics-server
```

2. Verify resource constraints:
```bash
kubectl get pod -n kube-system -l k8s-app=metrics-server -o yaml | grep resources: -A 8
```

3. Common fixes:
- Adjust resource limits if necessary
- Check node capacity
- Verify node selectors/taints

### 3. HPA Not Scaling

**Symptom:** HPA doesn't scale pods despite load

**Solutions:**
1. Check HPA status:
```bash
kubectl describe hpa -n <namespace> <hpa-name>
```

2. Verify metrics availability:
```bash
kubectl get --raw "/apis/metrics.k8s.io/v1beta1/nodes"
```

3. Common fixes:
- Ensure target metrics are being collected
- Check HPA configuration thresholds
- Verify scaling policies

### 4. TLS/Certificate Issues

**Symptom:** Certificate errors in logs

**Solutions:**
1. Check certificate configuration:
```bash
kubectl get apiservice v1beta1.metrics.k8s.io -o yaml
```

2. Verify TLS settings:
```bash
kubectl get deployment metrics-server -n kube-system -o yaml | grep "kubelet-" -A 5
```

3. Common fixes:
- Add --kubelet-insecure-tls flag (development only)
- Configure proper certificates
- Check certificate paths and permissions

### 5. Performance Issues

**Symptom:** High latency or resource usage

**Solutions:**
1. Check metrics-server resource usage:
```bash
kubectl top pod -n kube-system -l k8s-app=metrics-server
```

2. Monitor API server load:
```bash
kubectl get --raw /metrics | grep apiserver_request_total
```

3. Common fixes:
- Adjust scrape interval
- Scale metrics-server deployment
- Optimize resource requests/limits

## Quick Reference Commands

### Diagnostic Commands
```bash
# Check API registration
kubectl get apiservice v1beta1.metrics.k8s.io

# View metrics-server configuration
kubectl get deployment metrics-server -n kube-system -o yaml

# Check metrics availability
kubectl top nodes
kubectl top pods -A

# View detailed pod information
kubectl describe pod -n kube-system -l k8s-app=metrics-server
```

### Recovery Commands
```bash
# Restart metrics-server
kubectl rollout restart deployment metrics-server -n kube-system

# Scale metrics-server
kubectl scale deployment metrics-server -n kube-system --replicas=0
kubectl scale deployment metrics-server -n kube-system --replicas=1

# Force delete pod (if stuck)
kubectl delete pod -n kube-system <metrics-server-pod> --force --grace-period=0
```

## Prevention Checklist

- [ ] Regular monitoring of metrics-server logs
- [ ] Periodic verification of metrics availability
- [ ] Resource usage monitoring
- [ ] Certificate expiration checking
- [ ] Regular HPA configuration review

## Escalation Procedure

1. Check troubleshooting guide for common solutions
2. Review recent changes in ArgoCD
3. Check cluster events and logs
4. Contact DevOps team if issue persists
5. Open GitHub issue if bug is suspected