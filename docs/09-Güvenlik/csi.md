---
layout: default
title: CIS Benchmark
nav_order: 7
parent: Güvenlik
---

# CIS Benchmark

[Benchmark PDF](../kaynaklar/CIS Kubernetes 1.24 Benchmark v1.0.0 PDF.pdf)

https://www.cisecurity.org/benchmark/kubernetes

https://learn.microsoft.com/en-us/azure/aks/cis-kubernetes

## kube-bench

https://github.com/aquasecurity/kube-bench

```bash


$ kubectl apply -f https://github.com/aquasecurity/kube-bench/blob/main/job.yaml
job.batch/kube-bench created

$ kubectl get pods
NAME                      READY   STATUS              RESTARTS   AGE
kube-bench-j76s9   0/1     ContainerCreating   0          3s

# Wait for a few seconds for the job to complete
$ kubectl get pods
NAME                      READY   STATUS      RESTARTS   AGE
kube-bench-j76s9   0/1     Completed   0          11s

# The results are held in the pod's logs
kubectl logs kube-bench-j76s9
[INFO] 1 Master Node Security Configuration
[INFO] 1.1 API Server
...

```

## https://aquasecurity.github.io/trivy/v0.33/tutorials/kubernetes/cluster-scanning/