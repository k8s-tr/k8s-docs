---
layout: default
title:  Secrets
parent: Temel Kaynaklar
nav_order: 4
---

# Secrets (giz)

Kubernetes'de, özel bilgilerin, şifrelerin, kimlik doğrulama verilerinin ve diğer hassas verilerin depolanması için Secret adında bir yapı kullanılır. Secret, ConfigMap gibi anahtar-değer çiftleri kullanarak özel bilgileri depolar, ancak Secret verileri şifrelenir.

Secret'lar, YAML dosyaları aracılığıyla yapılandırılır ve uygulama içindeki konteynerlerin gizli bilgilere erişebilmesi için kullanılır. Secret'lar, Pod veya Deployment gibi yapılar tarafından referans edilebilir. Secret'lar, uygulama içinde kullanılan kimlik doğrulama verileri veya özel bilgileri gibi hassas verileri içerir.

Secret'lar, Kubernetes tarafından otomatik olarak şifrelenir ve depolanır. Bu sayede, Secret'lar güvenli bir şekilde depolanır ve uygulamanın güvenliği sağlanır. Ayrıca, Secret'lar, ConfigMap'ler gibi uygulama konfigürasyonlarını saklamak için de kullanılabilir.

Secret'lar, uygulamanın güvenliği için kritik önem taşır ve gizli bilgilerin yönetimini kolaylaştırır. Özellikle, uygulamaların bulut ortamlarında çalıştığı durumlarda, hassas verilerin güvenli bir şekilde depolanması ve yönetilmesi gereklidir. Secret'lar, uygulamanın güvenliği ve sürdürülebilirliği için önemli bir yapı taşıdır.

Secret verileri Kubernetes tarafından şifrelenmez ancak base64 ile kodlanır. Bu kodlama, Secret verilerinin depolanması ve yönetilmesi sırasında daha kolay bir şekilde işlenebilmesini sağlar. Ancak, base64 kodlaması verilerin gerçek şifrelenmesi olarak kabul edilemez, çünkü base64 kodlaması sadece verilerin okunabilirliğini ve anlaşılabilirliğini arttırır.

Bu nedenle, Secret verilerinin şifrelenmesi gerekiyorsa, önce verilerin gerçek şifreleme algoritmaları kullanılarak şifrelenmesi ve ardından bu şifrelenmiş verilerin base64 ile kodlanması gereklidir. Bu şekilde, Secret verileri güvenli bir şekilde saklanabilir ve yönetilebilir.

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
