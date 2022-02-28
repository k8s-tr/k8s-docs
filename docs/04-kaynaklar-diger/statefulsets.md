---
layout: default
title: StatefulSets
nav_order: 0
parent: Diğer Kaynaklar
---

# statefulsets

* Benzersiz ağ tanımlayıcıları.
* kalıcı depolama.
* Sıralı, graceful deployment ve ölçeklendirme.
* Sıralı, otomatik güncellemeler.

## sınırlandırmalar
* Bir storageclass ya da pv ye bağlı olmalı
* Silmek veriyi silmez
* Headless service'e ihtiyacı var. (Load balance yapmayan servis)
  * selector olursa tek ipye gider.
  * selector olmazsa endpointleri elle oluşturursun.
* silerken sağlıklı öldürmeyi yapmak için podların scaleini 0 yapın.
* 

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx
  labels:
    app: nginx
spec:
  ports:
  - port: 80
    name: web
  clusterIP: None
  selector:
    app: nginx
---
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: web
spec:
  selector:
    matchLabels:
      app: nginx # has to match .spec.template.metadata.labels
  serviceName: "nginx"
  replicas: 3 # by default is 1
  template:
    metadata:
      labels:
        app: nginx # has to match .spec.selector.matchLabels
    spec:
      terminationGracePeriodSeconds: 10
      containers:
      - name: nginx
        image: k8s.gcr.io/nginx-slim:0.8
        ports:
        - containerPort: 80
          name: web
        volumeMounts:
        - name: www
          mountPath: /usr/share/nginx/html
  volumeClaimTemplates:
  - metadata:
      name: www
    spec:
      accessModes: [ "ReadWriteOnce" ]
      storageClassName: "nfs-client"
      resources:
        requests:
          storage: 1Gi
```

https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/

