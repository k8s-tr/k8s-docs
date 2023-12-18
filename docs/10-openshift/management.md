---
layout: default
title: Management 
nav_order: 0
parent: Openshift
---

# Yönetim

## Web, CLI, API

## Projects and Users

## Resources
### Build

```yaml

apiVersion: build.openshift.io/v1
kind: BuildConfig
metadata:
  name: myapp-build
spec:
  source:
    type: Git
    git:
      uri: https://github.com/edib/oc-example.git
      # Optionally specify a branch, tag, or commit
      ref: master
  strategy:
    type: Docker
    dockerStrategy:
      # Optional fields like buildArgs can be specified here
  output:
    to:
      kind: ImageStreamTag
      name: 'merhaba-bash:latest'
  triggers:
    - type: GitLab
      gitlab:
        secret: secretReference
        # Other configuration for the GitLab webhook
    - type: ConfigChange
    - type: ImageChange

```

```yaml

apiVersion: build.openshift.io/v1
kind: BuildConfig
metadata:
  name: docker-build
  namespace: default
  labels:
    name: docker-build
spec:
  triggers:
  - type: GitHub
  - type: ImageChange
    imageChange: {}
  - type: ConfigChange
  source:
    type: Git
    git:
      uri: https://github.com/edib/oc-example.git
  strategy:
    type: Docker
  output:
    to:
      kind: ImageStreamTag
      name: merhaba-bash:latest
```
