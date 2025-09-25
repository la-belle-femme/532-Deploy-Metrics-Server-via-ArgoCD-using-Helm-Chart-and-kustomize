# Metrics Server Examples and Use Cases

## Common Use Cases

### 1. Horizontal Pod Autoscaling (HPA)

#### Basic CPU-based Scaling
```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: webapp
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: webapp
  minReplicas: 2
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 50
```

#### Memory-based Scaling
```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: webapp-memory
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: webapp
  minReplicas: 2
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: memory
      target:
        type: Utilization
        averageUtilization: 80
```

### 2. Resource Monitoring

#### Pod Resource Usage Script
```bash
#!/bin/bash
# monitor-pods.sh
namespace="your-namespace"
kubectl top pods -n $namespace | while read line
do
  if [[ $(echo $line | awk '{print $3}' | sed 's/Mi//') -gt 1000 ]]; then
    echo "High memory usage: $line"
  fi
done
```

#### Node Resource Check
```bash
#!/bin/bash
# check-node-resources.sh
kubectl top nodes | while read line
do
  cpu_usage=$(echo $line | awk '{print $3}' | sed 's/%//')
  if [[ $cpu_usage -gt 80 ]]; then
    echo "High CPU usage: $line"
  fi
done
```

### 3. Dashboard Integration

#### Grafana Query Examples
```
# CPU Usage Query
sum(rate(container_cpu_usage_seconds_total{container!=""}[5m])) by (pod)

# Memory Usage Query
sum(container_memory_usage_bytes{container!=""}) by (pod)
```

## Advanced Examples

### 1. Multi-Metric HPA

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: webapp-multi
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: webapp
  minReplicas: 2
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 50
  - type: Resource
    resource:
      name: memory
      target:
        type: Utilization
        averageUtilization: 80
  behavior:
    scaleUp:
      stabilizationWindowSeconds: 60
      policies:
      - type: Percent
        value: 100
        periodSeconds: 15
    scaleDown:
      stabilizationWindowSeconds: 300
      policies:
      - type: Percent
        value: 100
        periodSeconds: 15
```

### 2. Resource Quotas with Metrics

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: compute-resources
spec:
  hard:
    requests.cpu: "1"
    requests.memory: 1Gi
    limits.cpu: "2"
    limits.memory: 2Gi
```

### 3. Custom Monitoring Scripts

#### Comprehensive Resource Monitor
```bash
#!/bin/bash
# monitor-cluster-resources.sh

echo "=== Node Resource Usage ==="
kubectl top nodes

echo -e "\n=== Top 5 CPU-Intensive Pods ==="
kubectl top pods -A | sort -k3 -nr | head -5

echo -e "\n=== Top 5 Memory-Intensive Pods ==="
kubectl top pods -A | sort -k4 -nr | head -5

echo -e "\n=== Pods Exceeding Resource Limits ==="
kubectl top pods -A | while read line
do
  cpu=$(echo $line | awk '{print $3}' | sed 's/m//')
  mem=$(echo $line | awk '{print $4}' | sed 's/Mi//')
  
  if [[ $cpu -gt 1000 ]] || [[ $mem -gt 1024 ]]; then
    echo "$line"
  fi
done
```

## Integration Examples

### 1. ArgoCD Monitoring

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: monitoring-stack
spec:
  project: platform-tools
  source:
    repoURL: https://github.com/your-org/monitoring
    path: overlays/prod
    targetRevision: HEAD
  destination:
    server: https://kubernetes.default.svc
    namespace: monitoring
```

### 2. Alert Manager Configuration

```yaml
apiVersion: monitoring.coreos.com/v1
kind: PrometheusRule
metadata:
  name: metrics-server-alerts
spec:
  groups:
  - name: metrics-server
    rules:
    - alert: MetricsServerDown
      expr: up{job="metrics-server"} == 0
      for: 5m
      labels:
        severity: critical
      annotations:
        summary: "Metrics Server is down"
        description: "Metrics Server has been down for more than 5 minutes"
```

## Best Practices

1. Resource Requests/Limits
```yaml
resources:
  requests:
    cpu: 100m
    memory: 200Mi
  limits:
    cpu: 300m
    memory: 500Mi
```

2. Monitoring Intervals
- Default scrape interval: 15s
- HPA check interval: 15s
- Scale up stability window: 60s
- Scale down stability window: 300s

3. Production Configurations
- Enable TLS
- Set appropriate resource limits
- Configure proper RBAC
- Implement monitoring and alerting

## Testing Scenarios

1. Load Testing
```bash
# Generate CPU load
kubectl run -i --tty load-generator --rm --image=busybox --restart=Never -- /bin/sh -c "while true; do wget -q -O- http://your-service; done"
```

2. Memory Testing
```bash
# Generate memory load
kubectl run memory-test --image=polinux/stress --restart=Never -- stress --vm 1 --vm-bytes 150M
```