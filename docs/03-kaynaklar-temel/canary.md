---
layout: default
title:  Kanarya Deployment
parent: Temel Kaynaklar
nav_order: 10
---

# Kanarya Deployment

```yaml

apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: myboot
  name: myboot
spec:
  replicas: 1
  selector:
    matchLabels:
      app: myboot
  template:
    metadata:
      labels:
        app: myboot
    spec:
      containers:
      - name: myboot
        image: quay.io/rhdevelopers/myboot:v1
        ports:
          - containerPort: 8080
---
apiVersion: v1
kind: Service
metadata:
  name: myboot
  labels:
    app: myboot    
spec:
  ports:
  - name: http
    port: 8080
  selector:
    app: myboot
--- 
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: myboot
  name: myboot2
spec:
  replicas: 1
  selector:
    matchLabels:
      app: myboot
  template:
    metadata:
      labels:
        app: myboot
    spec:
      containers:
      - name: myboot
        image: quay.io/rhdevelopers/myboot:v3
        ports:
          - containerPort: 8080
```


```