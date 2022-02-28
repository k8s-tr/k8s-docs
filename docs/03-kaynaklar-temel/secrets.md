---
layout: default
title:  Secrets
parent: Temel Kaynaklar
nav_order: 4
---

# Secrets (giz)

Uygulamayı Kurun

```sh
kubectl apply -f https://raw.githubusercontent.com/redhat-scholars/kubernetes-tutorial/master/apps/kubefiles/myboot-deployment.yml

kubectl apply -f https://raw.githubusercontent.com/redhat-scholars/kubernetes-tutorial/master/apps/kubefiles/myboot-deployment-configuration.yml
```

secret oluştur

```sh
kubectl create secret generic mysecret --from-literal=user='MyUserName' --from-literal=password='mypassword'

kubectl get secrets

kubectl describe secret mysecret

kubectl get secret mysecret -o yaml

echo 'bXlwYXNzd29yZA==' | base64 --decode

kubectl get secret mysecret -o jsonpath='{.data.password}' | base64 --decode
```

gizler dizin bağlamakla podlara bağlanır

```yaml
        volumeMounts:
          - name: mysecretvolume
            mountPath: /mystuff/mysecretvolume
```

```sh
kubectl replace -f https://raw.githubusercontent.com/redhat-scholars/kubernetes-tutorial/master/apps/kubefiles/myboot-deployment-configuration-secret.yml
```

poda bakalım
```sh
PODNAME=$(kubectl get pod -l app=myboot -o name)
kubectl exec $PODNAME -- cat /mystuff/secretstuff/password
```
