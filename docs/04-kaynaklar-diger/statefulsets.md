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
      resources:
        requests:
          storage: 1Gi
```

https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/



```yaml

apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: example-statefulset
spec:
  serviceName: "example"
  replicas: 2
  selector:
    matchLabels:
      app: example
  template:
    metadata:
      labels:
        app: example
    spec:
      containers:
      - name: example-container
        image: quay.io/rhdevelopers/myboot:v1
        volumeMounts:
        - name: example-volume
          mountPath: /data
  volumeClaimTemplates:
  - metadata:
      name: example-volume
    spec:
      accessModes: [ "ReadWriteOnce" ]
      resources:
        requests:
          storage: 1Gi
```


```yaml

# Example of PostgreSQL cluster
apiVersion: postgresql.cnpg.io/v1
kind: Cluster
metadata:
  name: cluster-example
spec:
  instances: 3
  storage:
    size: 1Gi

```


## shared disk

```yaml

kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: shared-disk
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 1Gi


---

kind: Deployment
apiVersion: apps/v1
metadata:
  name: shared-disk-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: postgres
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: postgres
    spec:
      volumes:
        - name: shared-disk-storage
          persistentVolumeClaim:
            claimName: shared-disk
      containers:
        - name: postgres
          image: 'quay.io/rhdevelopers/myboot:v1'
          volumeMounts:
            - name: shared-disk-storage
              mountPath: /data

```


# nfs client ile

```yaml

kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: nfs-disk
spec:
  storageClass: nfs-client
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 1Gi

--- 

kind: Deployment
apiVersion: apps/v1
metadata:
  name: shared-disk-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: postgres
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: postgres
    spec:
      volumes:
        - name: shared-disk-storage
          persistentVolumeClaim:
            claimName: nfs-disk
      containers:
        - name: postgres
          image: 'quay.io/rhdevelopers/myboot:v1'
          volumeMounts:
            - name: shared-disk-storage
              mountPath: /data
```