---
title:  ARGO-CD
layout: default
parent: Ek Araçlar
---

# argocd
Hem declarative hem de web ui özelliği olan kubernetes cd aracı

## Ne işe yarar?

* Gitops yaklaşımı sağlar
* Uygulamaların belirtilen hedef ortamlara otomatik dağıtımı
* Çoklu yapılandırma yönetimi/şablon oluşturma araçları için destek (Kustomize, Helm, Ksonnet, Jsonnet, düz-YAML)
* Birden çok kümeyi yönetme ve dağıtma yeteneği
* SSO Entegrasyonu (OIDC, OAuth2, LDAP, SAML 2.0, GitHub, GitLab, Microsoft, LinkedIn)
* Yetkilendirme ile çoklu müşteri ve RBAC özellikleri
* Gitteki istediğiniz branche bağlayarak farklı testleri otomatik gerçekleştirme
* Çalıştırılmış bir uygulamayı geri alma/her yerden geri alma
* Uygulama kaynaklarının sağlık durumu analizi (web ui ve cliden kontrol)
* Git ile kubernetes ortamları arasındaki farklı tespit etme ve görselleştirme
* Uygulamaların istenen duruma otomatik veya manuel olarak eşitlenmesi
* Uygulama etkinliğinin gerçek zamanlı görünümünü sağlayan Web Kullanıcı Arayüzü
* Otomasyon ve CI entegrasyonu için CLI
* Web hook entegrasyonu (GitHub, BitBucket, GitLab)
* Otomasyon için erişim tokenları
* Karmaşık uygulama sunumlarını desteklemek için PreSync, Sync, PostSync hookları (ör. mavi/yeşil ve kanarya yükseltmeleri)
* Uygulama olayları ve API çağrıları için denetim kayıtları
* Prometheus metrikleri
* Helm deployment (Nginx ingress helm redis vb.)
* Argocd Notifications ile alarm üreterek bir çok araca otomatik bildirim gönderme


![argocd mimari](/docs/kaynaklar/argo-cd-mimari.png)

## Kurulum

```
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

  # web değil sadece cli istersek
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/core-install.yaml

  # web sunucuya ulaşmak için
kubectl patch svc argocd -server -n argocd -p '{"spec": {"type": "NodePort"}}'

# login parolasını almak(kullanıcı adı admin)
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath='{.data.password}' | base64 -d && echo

# webui a gitmek için
https: //nodeip:nodeport


Local admini devre dışı bırakmak için

apiVersion: v1
kind: ConfigMap
metadata:
  name: argocd-cm
...
data:
  ...
  admin.enabled: "false"

```


## Declarative Örnek

* Hangi repodan hangi revisiondan veriyi alacak?
* Hangi kube kümesine kuracak?


```
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: argocd-demo
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/justmeandopensource/argocd-demo
    targetRevision: HEAD
    path: yamls
  destination:
    server: https://kubernetes.default.svc
    namespace: default

```


## Komut satırı kullanımı

```

argocd login <argocd-server adres>
argocd account list
argocd account get --account <yukarıdaki kullanıcı>
argocd account update-password   --account <yukarıdaki kullanıcı>   --current-password <admin parolası>   --new-password <bir password>


ci-cd sürecinde kullanılacak token burada üretilir. Login olduktan sonra hiç parametre vermeden aşağıdaki komut çalıştırılırsa never expirede token üretir. 
argocd account generate-token 


Bu token gitlab-ci variables altında argocd config dosyası içine konularak kullanılır

contexts:
- name: argocd-server.argocd
  server: argocd-server.argocd
  user: argocd-server.argocd
current-context: argocd-server.argocd
servers:
- grpc-web-root-path: ""
  insecure: true
  server: argocd-server.argocd
users:
- auth-token: <token>
  name: argocd-server.argocd

```

## rbac yapısı

https://www.opsmx.com/blog/how-to-configure-rbac-in-argo-cd/ (kullanıcı oluşturma)

```

kubectl edit cm argocd-cm -n argocd

apiVersion: v1
data:
  accounts.admin: apiKey, login
  account.<kullanıcı>: apiKey, login #  yeni kullanıcı
  exec.enabled: "true" # terminal çalışsın diye
  ...
kind: ConfigMap
...
  name: argocd-cm
  namespace: argocd



argocd login <ARGOCD-URL>

argocd account list

argocd account update-password --account <kullanıcı> --new-password <parola>



apiVersion: v1
data:
  default.policy: role:''
  policy.csv: |
    p, <kullanıcı>, *, *, <proje>/*, allow
kind: ConfigMap
metadata:
  name: argocd-rbac-cm
  namespace: argocd

```


## dex entegrasyonu ve ldap login

### dex config
```yaml

apiVersion: v1
kind: ConfigMap
metadata:
  name: argocd-cm
  namespace: argocd
data:
  dex.config: |
    connectors:
    - type: ldap
      name: LDAP
      id: ldap
      config:
        host: "mydc.local"
        insecureNoSSL: true
        insecureSkipVerify: true
        bindDN: "$dex.ldap.bindDN"
        bindPW: "$dex.ldap.bindPW"
        usernamePrompt: Username
        userSearch:
          baseDN: "OU=_ORGANIZATION,DC=mydc,DC=local"
          filter: ""
          username: sAMAccountName
          idAttr: sAMAccountName
          emailAttr: mail
          nameAttr: givenName
        groupSearch:
          baseDN: "OU=_ORGANIZATION,DC=mydc,DC=local"
          filter: "(objectclass=group)"
          userAttr: DN
          groupAttr: member
          nameAttr: cn

```

### rbac config

```yaml

apiVersion: v1
kind: ConfigMap
metadata:
  name: argocd-rbac-cm
  namespace: argocd
data:
  policy.default: role:''
  policy.csv: |
   g, birgrup, role:birrole

```


Kaynaklar
* [ Just me and Opensource - [ Kube 85.1 ] Argo CD continuous deployment to Kubernetes - Part 1 ](https://www.youtube.com/watch?v=HX24uMKmJRw&list=PL34sAs7_26wMW4bWKnMIfEd87aPuw75by)

