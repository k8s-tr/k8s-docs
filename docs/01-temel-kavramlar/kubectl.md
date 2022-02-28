---
layout: default
title: kubectl
parent: Temel Kavramlar
nav_order: 5
---

# kubectl: Asıl Kubernetes İstemcisi

```sh
kubectlecho $KUBECONFIG

/home/user/.kube/config

kubectl config view


apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://apiserver:6443
  name: cluster.local

```
* Nodları incele

```sh
kubectl get nodes

# etiketleriyle göster (show-labels'ın çok faydalı olduğu yerler var)
kubectl get nodes --show-labels

# ad alanlarını gör
kubectl get namespaces

```

* Herşeyi gör

```sh
kubectl get pods [--all-namespaces|-A]
kubectl get pods [--all-namespaces|-A] --show-labels
kubectl get pods [--all-namespaces|-A] -o wide

```

* Birşeyler kur

```sh
kubectl create namespace mystuff
kubectl config set-context --current --namespace=mystuff

kubectl create deployment myapp --image=quay.io/rhdevelopers/quarkus-demo:v1

```

* Birşeyler kurulumunu izleyerek bekle

```sh
watch kubectl get events --sort-by=.metadata.creationTimestamp

LAST SEEN   TYPE     REASON              OBJECT                        MESSAGE
<unknown>   Normal   Scheduled           pod/myapp-5dcbf46dfc-ghrk4    Successfully assigned mystuff/myapp-5dcbf46dfc-ghrk4 to g
cp-5xldg-w-a-5ptpn.us-central1-a.c.ocp42project.internal
29s         Normal   SuccessfulCreate    replicaset/myapp-5dcbf46dfc   Created pod: myapp-5dcbf46dfc-ghrk4
29s         Normal   ScalingReplicaSet   deployment/myapp              Scaled up replica set myapp-5dcbf46dfc to 1
21s         Normal   Pulling             pod/myapp-5dcbf46dfc-ghrk4    Pulling image "quay.io/burrsutter/quarkus-demo:1.0.0"
15s         Normal   Pulled              pod/myapp-5dcbf46dfc-ghrk4    Successfully pulled image "quay.io/burrsutter/quarkus-dem
o:1.0.0"
15s         Normal   Created             pod/myapp-5dcbf46dfc-ghrk4    Created container quarkus-demo
15s         Normal   Started             pod/myapp-5dcbf46dfc-ghrk4    Started container quarkus-demo

```

### Nesneler oluştur

#### Deployments


```sh
kubectl get deployments


NAME    READY   UP-TO-DATE   AVAILABLE   AGE
myapp   1/1     1            1           95s

```

#### Replicasets

```sh
kubectl get replicasets

NAME               DESIRED   CURRENT   READY   AGE
myapp-5dcbf46dfc   1         1         1       2m1s

```

#### Pods

```sh
kubectl get pods --show-labels

NAME                     READY   STATUS    RESTARTS   AGE     LABELS
myapp-5dcbf46dfc-ghrk4   1/1     Running   0          2m18s   app=myapp,pod-template-hash=5dcbf46dfc

```

#### Logs 

```sh
kubectl logs -l app=myapp

2020-03-22 14:41:30,497 INFO  [io.quarkus] (main) Quarkus 0.22.0 started in 0.021s. Listening on: http://0.0.0.0:8080
2020-03-22 14:41:30,497 INFO  [io.quarkus] (main) Installed features: [cdi, resteasy]

```

#### Expose: Bir servisi dışarı aç

```sh
kubectl expose deployment myapp --port=8080 --type=LoadBalancer

watch kubectl get services

NAME    TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
myapp   LoadBalancer   172.30.103.41   <pending>     8080:31974/TCP   4s

```

#### Uygulamayı ölçeklendir

```sh
watch kubectl get pods

kubectl scale deployment myapp --replicas=3

#v1
kubectl set image deployment/myapp quarkus-demo=quay.io/rhdevelopers/myboot:v1
#v2
kubectl set image deployment/myapp quarkus-demo=quay.io/rhdevelopers/myboot:v2
#v3
kubectl set image deployment/myapp quarkus-demo=quay.io/rhdevelopers/quarkus-demo:v1

```

#### Temizlik

```sh
kubectl delete namespace mystuff
kubectl config set-context --current --namespace=default

```





