---
layout: default
title: Kubespray Hardening
nav_order: 9
parent: Güvenlik
---

## Kubespray Hardening

* CIS kurallarına göre hardening parametreleri 
https://github.com/kubernetes-sigs/kubespray/blob/master/docs/hardening.md

Bir deployment veya pod oluşturulmaya çalışılırsa aşağıdaki hatayı verecektir

```log

Error from server (Forbidden): pods "test" is forbidden: violates PodSecurity "restricted:v1.27": allowPrivilegeEscalation != false (container "test" must set securityContext.allowPrivilegeEscalation=false), unrestricted capabilities (container "test" must set securityContext.capabilities.drop=["ALL"]), runAsNonRoot != true (pod or container "test" must set securityContext.runAsNonRoot=true), seccompProfile (pod or container "test" must set securityContext.seccompProfile.type to "RuntimeDefault" or "Localhost")
 how to make exception

```

* Default hardening kurallarına uygun deployment tanımı
* bu kurallar için uygun imajında root kullanıcısı ile çalışmıyor olması gerekir. Yoksa öyle bir hata verecektir. 

```bash 

kubectl describe pod secure-deployment

  Warning  Failed     12s (x8 over 98s)   kubelet            Error: container has runAsNonRoot and image will run as root (pod: "secure-deployment-85c9947bdc-kgtk6_test(9e6a9b9f-aa47-4625-9bd6-ad519e03f127)", container: secure-container)

```

```yaml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: secure-deployment
  labels:
    app: secure-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: secure-app
  template:
    metadata:
      labels:
        app: secure-app
    spec:
      containers:
      - name: secure-container
        image: nginxinc/nginx-unprivileged:latest
        securityContext:
          allowPrivilegeEscalation: false
          capabilities:
            drop: ["ALL"]
          runAsNonRoot: true
          seccompProfile:
            type: RuntimeDefault


```

* istisnalar sadece ns düzeyinde yaratılabilir. tekil pod/deployment için çalışmaz

```yaml

apiVersion: v1
kind: Namespace
metadata:
  labels:
    kubernetes.io/metadata.name: loy
    pod-security.kubernetes.io/audit: baseline
    pod-security.kubernetes.io/enforce: baseline
    pod-security.kubernetes.io/exempt: "true"
    pod-security.kubernetes.io/warn: baseline
  name: istisna-ns


```

### Kaynaklar
* https://kubernetes.io/docs/concepts/security/pod-security-admission/
* https://kubernetes.io/docs/tasks/configure-pod-container/enforce-standards-namespace-labels/