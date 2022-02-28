---
layout: default
title:  Ingress
nav_order: 1
parent: Diğer Kaynaklar
---

# ingress 

Ingress için, L7 (özellikle http protokolü) yeteneği olan service diyebiliriz.
Ingress özelliği, bir ingress controller(IC) kaynağına ihtiyaç duyar. Kubernetes'e IC'yi başka bir sağlayıcıdan kurmamız gerkeir. En popüler olanı ingress nginx controller'dır. 

```sh
kubectl apply -f https://gist.github.com/rockcyj/a298fafad969e5ca8e77c3e66fa815fe/raw/57254e34049c963cf83a4619b0d2fb6d5b21b24f/ingress-nginx-controller-1.yaml
```


```yaml

kind: Ingress
apiVersion: extensions/v1beta1
metadata:
  name: dashboard-ingress
  namespace: kube-system
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/backend-protocol: HTTPS
    nginx.ingress.kubernetes.io/ssl-passthrough: 'true'
    nginx.ingress.kubernetes.io/ssl-redirect: 'true'
spec:
  rules:
    - host: dashboard.mylocal
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              serviceName: kubernetes-dashboard
              servicePort: 443

```


```yaml

kind: Ingress
apiVersion: extensions/v1beta1
metadata:
  name: grafana-ingress
  namespace: monitoring
  annotations:
    kubernetes.io/ingress.class: nginx
spec:
  rules:
    - host: grafana.mylocal
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              serviceName: loki-stack-grafana
              servicePort: 80
```

https://redhat-scholars.github.io/kubernetes-tutorial/kubernetes-tutorial/ingress.html


* ingress yaml dosyaları
https://github.com/redhat-scholars/kubernetes-tutorial/tree/master/apps/kubefiles