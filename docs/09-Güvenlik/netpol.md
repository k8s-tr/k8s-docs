---
layout: default
title: Güvenlik
nav_order: 1
has_children: true
---


* namespaced 
### deny all (first policy)

```bash
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: default-deny
spec:
  podSelector: {}
  policyTypes:
  - Ingress
  - Egress

```


1. A Pod with the label `app: my-app`:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-app-pod
  labels:
    app: my-app
spec:
  containers:
  - name: alpine
    image: alpine:latest
```

2. A Pod with the label `role: frontend`:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: frontend-pod
  labels:
    role: frontend
spec:
  containers:
  - name: alpine
    image: alpine:latest
```

3. A Pod with the label `role: backend`:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: backend-pod
  labels:
    role: backend
spec:
  containers:
  - name: alpine
    image: alpine:latest
```

** network policy **

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: my-network-policy
spec:
  podSelector:
    matchLabels:
      app: my-app
  policyTypes:
  - Ingress
  - Egress
  ingress:
  - from:
    - podSelector:
        matchLabels:
          role: frontend
    ports:
    - protocol: TCP
      port: 80
  egress:
  - to:
    - podSelector:
        matchLabels:
          role: backend
    ports:
    - protocol: TCP
      port: 80

```

**Editor**

https://editor.networkpolicy.io/