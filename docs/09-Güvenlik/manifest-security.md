---
layout: default
title: Manifest Güvenliği
nav_order: 6
parent: Güvenlik
---


# manifest security

* Pod or Container level. 

### Example Deployment Manifest

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: secure-deployment
  labels:
    app: secure-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: secure-app
  template:
    metadata:
      labels:
        app: secure-app
    spec:
      securityContext:
        fsGroup: 1000
        runAsUser: 1000
        runAsGroup: 3000
        runAsNonRoot: true
      containers:
      - name: secure-container
        image: your-image-name:tag
        securityContext:
          readOnlyRootFilesystem: true
          allowPrivilegeEscalation: false
          privileged: false
          capabilities:
            drop:
            - ALL
        ports:
        - containerPort: 8080
```


- `fsGroup: 1000`: Defines the group ID for the volume’s filesystem.
- `runAsUser: 1000`: Specifies the UID to run the entrypoint of the container.
- `runAsGroup: 3000`: Specifies the GID for the process's group.
- `runAsNonRoot: true`: Ensures that the container process is not run as the root user.

### Container-Level `SecurityContext`:

- `readOnlyRootFilesystem: true`: Makes the container's filesystem read-only.
- `allowPrivilegeEscalation: false`: Prevents the container from acquiring more privileges than its parent process.
- `privileged : false`: Ensures the container is not privileged.
- `capabilities: drop: - ALL`: Drops all capabilities from the container process.


# kyverno reference

```yaml

apiVersion: kyverno.io/v1
kind: Policy
metadata:
  name: enforce-security-context
spec:
  validationFailureAction: Enforce
  rules:
  - name: enforce-runAsNonRoot
    match:
      resources:
        kinds:
        - Pod
    validate:
      message: "The Pod must run as a non-root user"
      pattern:
        spec:
          securityContext:
            runAsNonRoot: true
          containers:
          - name: "*"
            securityContext:
              runAsNonRoot: true
              readOnlyRootFilesystem: true
            image: "*"
          initContainers:
          - name: "*"
            securityContext:
              runAsNonRoot: true
              readOnlyRootFilesystem: true
            image: "*"

```


# Hostpath Security

```yaml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: example-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: example
  template:
    metadata:
      labels:
        app: example
    spec:
      volumes:
        - name: hostpath-volume
          hostPath:
            path: /path/on/host
            type: DirectoryOrCreate
      initContainers:
        - name: init-mkdir-chown
          image: busybox:1.32
          command: ['sh', '-c', 'mkdir -p /data/dir && chown 1000:1000 /data/dir']
          volumeMounts:
            - name: hostpath-volume
              mountPath: /data
      containers:
        - name: main-container
          image: your-main-container-image
          securityContext:
            runAsUser: 1000
            runAsGroup: 1000
          volumeMounts:
            - name: hostpath-volume
              mountPath: /data
```