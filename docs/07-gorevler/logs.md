---
layout: default
parent: Görevler
nav_order: 4
title: Günlük Kayıtları
---


# Günlük Kayıtları

https://kubernetes.io/docs/concepts/cluster-administration/logging/

* Kullanılabilecek araçlar
    - filebeat
    - fluentd
    - fluentbit

```sh

cat <<EOF | kubectl apply -f -
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
        env: dev
    spec:
      containers:
      - name: myapp
        image: quay.io/rhdevelopers/myboot:v1
        imagePullPolicy: Always
        ports:
        - containerPort: 8080
EOF

# kurulum ayağa kalktı mı bakıyoruz.
watch kubectl get deployments

NAME                             READY   STATUS    RESTARTS   AGE
my-deployment-5dc67997c7-5bq4n   1/1     Running   0          34s
my-deployment-5dc67997c7-m7z9f   1/1     Running   0          34s
my-deployment-5dc67997c7-s4jc6   1/1     Running   0          34s

```

Logları incelemek için 

```sh
kubectl logs my-deployment-5dc67997c7-m7z9f [-f]
```

## Log Yardımcı Araçları
* [stern](https://github.com/wercker/stern) - Kubernetes için çoklu bölme ve kapsayıcı günlüğü takibi
* [kail](https://github.com/boz/kail) - kubernetes günlük görüntüleyici

