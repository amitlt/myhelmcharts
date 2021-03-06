# nodelocaldns

### Description
https://github.com/kubernetes/dns/tree/master/cmd/node-cache

Helm chart with support of graceful config reload.  

## Installing the Chart

Render templates before usage:
```bash
helm template . -f dev_values.yaml
```

Deploy to Dev cluster:
```bash
kubectl config current-context
helm upgrade --install --force node-local-dns . --namespace kube-system --tiller-namespace kube-system --wait --timeout 180 --debug  --values dev_values.yaml
```

Deploy to Prod cluster:
```bash
kubectl config current-context
helm upgrade --install --force node-local-dns . --namespace kube-system --tiller-namespace kube-system --wait --timeout 180 --debug  --values prod_values.yaml
```

After successful deploy, change kubelet's `--cluster-dns` to value of chart's `localDNSAddress`. In the Kubespray this value called `manual_dns_server` (also needs `dns_mode: manual`).

## Configuration

| Parameter  | Description | Defaults
|:-----------|:------------|---------|
| `namespace`  |             | kube-system |
| `serviceaccount` |         | node-local-dns |
| `clusterDomain` |          | cluster.local |
| `localDNSAddress` | IP address of node-local-dns pod (hostNetwork mode), must used at kubelet's flag | 169.254.25.10 |
| `clusterDNSAddress` | IP address of upstream cluster DNS service     | 10.140.0.3 |
| `image.repository`        | Docker image of node-local-dns         | k8s.gcr.io/k8s-dns-node-cache |
| `image.tag`    | Docker image's tag | 1.15.8 |
| `clusterDNSSelector` | selector for cluster DNS pods (to reach them IP's) | k8s-app: coredns |
| `res`          | node-local-dns pod's resources |
| `profilingPort`    | enables pprof profiling at selected hostPort (nodelocaldns runs on hostNetwork) if your image built with pprof. Don't use with images < 1.15.8 | 6068

## Profiling

```bash
kubectl -n kube-system port-forward node-local-dns-nzdsh 6068:6068
```

Access profiler with browser:
```
http://localhost:6068/debug/pprof/
```

