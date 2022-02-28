---
layout: default
title:  Service
parent: Temel Kaynaklar
nav_order: 2
---

# Service

**Yapı** 

https://medium.com/avmconsulting-blog/service-types-in-kubernetes-24a1587677d6

https://octopus.com/docs/deployments/kubernetes/deploy-container

https://matthewpalmer.net/kubernetes-app-developer/articles/service-kubernetes-example-tutorial.html


Uygun ad alanına geçin
```sh
kubectl config set-context --current --namespace=myspace
```
Deploymentları kontrol edin.

```sh
kubectl get deployments

NAME                      READY   UP-TO-DATE   AVAILABLE   AGE
quarkus-demo-deployment   3/3     3            3           8m33s

# rsleri kontrol edin
kubectl get rs

NAME                                 DESIRED   CURRENT   READY   AGE
quarkus-demo-deployment-5979886fb7   3         3         3       8m56s

# podlara bak

kubectl get pods

NAME                                       READY   STATUS    RESTARTS   AGE
quarkus-demo-deployment-5979886fb7-c888m   1/1     Running   0          9m17s
quarkus-demo-deployment-5979886fb7-gdtnz   1/1     Running   0          9m17s
quarkus-demo-deployment-5979886fb7-grf59   1/1     Running   0          9m17s
```

Bir servis oluşturun. 

```sh
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Service
metadata:
  name: the-service
spec:
  selector:
    app: quarkus-demo
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
  type: NodePort 
EOF

# durumunu izliyoruz.
watch kubectl get services

NAME    TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
myapp   LoadBalancer   172.30.103.41   <pending>     8080:31974/TCP   4s
```





