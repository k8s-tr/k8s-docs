---
layout: default
title: Etiketler
nav_order: 1
parent: Önemli Kaynaklar
---

# Label

Servicelerin deploymentlarla ilişkisini test ediyoruz.

```sh
kubectl create namespace funstuff
kubectl config set-context --current --namespace=funstuff
```

1. Uygulamayı Kur

```sh
# app: mypython

cat <<EOF | kubectl apply -f -
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mypython-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mypython
  template:
    metadata:
      labels:
        app: mypython
    spec:
      containers:
      - name: mypython
        image: quay.io/rhdevelopers/mypython:v1
        ports:
        - containerPort: 8000
EOF

```

2. Uygulamayı Kur

```sh
# app: mynode

cat <<EOF | kubectl apply -f -
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mynode-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mynode
  template:
    metadata:
      labels:
        app: mynode
    spec:
      containers:
      - name: mynode
        image: quay.io/rhdevelopers/mynode:v1
        ports:
        - containerPort: 8000
EOF

# ayağa kalkmasını bekleyelim. 
watch kubectl get pods --show-labels
```

Servisi kuralım

```sh

selector değerine dikkat

cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Service
metadata:
  name: my-service
  labels:
    app: mystuff
spec:
  ports:
  - name: http
    port: 8000
  selector:
    inservice: mypods
  type: LoadBalancer
EOF
```

Kurduğumuz servisin açıklamalarına bakalım

```sh

# hiç endpoints yok
kubectl describe service my-service

kubectl get endpoints

NAME         ENDPOINTS   AGE
my-service   <none>      2m6s
```

servisin ip adresini ve portunun alalım. 

```sh

curl <svc_ip>:<svc_port>

curl: (7) Failed to connect to 35.224.233.213 port 8000: Connection refused

```

deploymentlardaki podlara elle serviceteki labelı ekliyoruz

```sh

# 1. uygulama
kubectl label pod -l app=mypython inservice=mypods

curl <svc_ip>:<svc_port>

Python Hello on mypython-deployment-578c56d87-rhlf8

#2. uygulama

kubectl label pod -l app=mypython inservice=mypods


# servisin artık 2 deploymenta da gittiğini görüyoruz.

while true; do curl 10.152.183.159:8000; sleep .3; done

Python Hello on mypython-deployment-578c56d87-rhlf8
Go Hello on mygo-deployment-d49998955-6fgvq
Go Hello on mygo-deployment-d49998955-6fgvq
Python Hello on mypython-deployment-578c56d87-rhlf8
Go Hello on mygo-deployment-d49998955-6fgvq
Go Hello on mygo-deployment-d49998955-6fgvq
Python Hello on mypython-deployment-578c56d87-rhlf8

```

Benzer şekilde label tanımlarını böyle kaldırabiliriz.

```sh

kubectl label pod -l app=mypython inservice-

```

