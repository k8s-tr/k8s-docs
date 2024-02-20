---
layout: default
title: Openhift build and push 
nav_order: 1
parent: Openshift
---

# Openhift build and push


# github örneği

* https://github.com/edib/oc-example.git

```bash
oc whoami

podman login -u kubeadmin -p $(oc whoami -t) default-route-openshift-image-registry.apps-crc.testing --tls-verify=false

# eğer docker kullanılırsa (docker daemonda değişiklik gerekiyor)
docker login -u kubeadmin -p $(oc whoami -t) default-route-openshift-image-registry.apps-crc.testing


$ oc new-project demo

podman pull quay.io/libpod/alpine

podman tag alpine:latest default-route-openshift-image-registry.apps-crc.testing/demo/alpine:latest

podman push default-route-openshift-image-registry.apps-crc.testing/demo/alpine:latest --tls-verify=false

oc get is

$ oc run demo --image=alpine --command -- sleep 600s

```
