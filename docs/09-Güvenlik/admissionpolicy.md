---
layout: default
title: Admission Policy
nav_order: 2
parent: Güvenlik
---

```bash

kubectl create -f https://github.com/kyverno/kyverno/releases/download/v1.10.3/install.yaml

```
# Validation

```bash
kubectl create -f- << EOF
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: require-labels
spec:
  validationFailureAction: Enforce
  rules:
  - name: check-team
    match:
      any:
      - resources:
          kinds:
          - Pod
    validate:
      message: "label 'team' is required"
      pattern:
        metadata:
          labels:
            team: "?*"
EOF


```


```bash
kubectl create deployment nginx --image=nginx


kubectl run nginx --image nginx --labels team=backend

kubectl get policyreport
```

# Mutation

```bash
kubectl create -f- << EOF
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: add-labels
spec:
  rules:
  - name: add-team
    match:
      any:
      - resources:
          kinds:
          - Pod
    mutate:
      patchStrategicMerge:
        metadata:
          labels:
            +(team): bravo
EOF

kubectl run redis --image redis

kubectl get pod redis --show-labels

kubectl run newredis --image redis -l team=alpha

kubectl get pod myredis --show-labels

kubectl delete clusterpolicy add-labels

```

# policy vs clusterpolicy

```yaml
apiVersion: kyverno.io/v1
kind: Policy
metadata:
  name: add-labels
spec:
  rules:
  - name: add-team
    match:
      any:
      - resources:
          kinds:
          - Pod
    mutate:
      patchStrategicMerge:
        metadata:
          labels:
            +(team): bravo 

```

```bash
kubectl apply -f your-policy-file.yaml -n my-namespace

```

## Generation

```bash
kubectl -n default create secret docker-registry regcred \
  --docker-server=myinternalreg.corp.com \
  --docker-username=john.doe \
  --docker-password=Passw0rd123! \
  --docker-email=john.doe@corp.com


kubectl create -f- << EOF
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: sync-secrets
spec:
  rules:
  - name: sync-image-pull-secret
    match:
      any:
      - resources:
          kinds:
          - Namespace
    generate:
      apiVersion: v1
      kind: Secret
      name: regcred
      namespace: "{{request.object.metadata.name}}"
      synchronize: true
      clone:
        namespace: default
        name: regcred
EOF


kubectl create ns mytestns


kubectl -n mytestns get secret

kubectl delete clusterpolicy sync-secrets

```


## deny unkown repositories

```yaml

apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: allowed-repo
spec:
  validationFailureAction: Enforce
  rules:
  - name: check-registries
    match:
      resources:
        kinds:
        - Pod
    validate:
      message: "Registry not allowed"
      pattern:
        spec:
          containers:
          - image: "docker.io/* | quay.io/*"


```

## deny privileged pods

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: disallow-privileged-containers
spec:
  validationFailureAction: Enforce
  background: false
  rules:
    - name: prevent-privileged-containers
      match:
        resources:
          kinds:
            - Pod
      validate:
        message: "Privileged containers are not allowed"
        pattern:
          spec:
            containers:
              - =(securityContext):
                  =(privileged): false


```