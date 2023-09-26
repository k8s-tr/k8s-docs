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

apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: minimal-ingress
  namespace: kubernetes-dashboard
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  ingressClassName: nginx
  rules:
  - host: dashboard.local
    http:
          paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: kubernetes-dashboard
                port:
                  number: 80

---

# network-policy-tutorial   frontend


apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: minimal-ingress
  namespace: network-policy-tutorial 
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  ingressClassName: nginx
  rules:
  - http:
          paths:
          - path: /frontend
            pathType: Prefix
            backend:
              service:
                name: frontend
                port:
                  number: 80

```


```yaml

kind: Ingress
apiVersion: networking.k8s.io/v1
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