---
layout: default
title: OC Client 
nav_order: 4
parent: Openshift
---


# kullanımlar


```bash

oc create deployment myapp --image=quay.io/rhdevelopers/quarkus-demo:v1

oc expose deploy myapp 

oc expose service myapp

oc get route 

```