---
layout: default
title: Volumes
nav_order: 0
parent: Diğer Kaynaklar
---

# volumes

https://redhat-scholars.github.io/kubernetes-tutorial/kubernetes-tutorial/volumes-persistentvolumes.html

## Static 
Statik provizyon, küme yöneticilerinin mevcut depolama aygıtını aktif eder. Bu şekilde yapıldığında PV ve PVC manuel olarak sağlanmalıdır.

## Dynamic Provisioning
kullanıcı istediği zaman depolama aracını kullanabilir. Bunun için storageclass lazım. Bu storageclasstan alan isteyen bir pvc. 

### static pv
https://github.com/edib/kubernetes/tree/master/yamls/nfs-pv

pv

```yaml

apiVersion: v1
kind: PersistentVolume
metadata:
  name: nfs-pv
spec:
  capacity:
    storage: 10Gi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteMany
  persistentVolumeReclaimPolicy: Recycle
  storageClassName: nfs
  mountOptions:
    - hard
    - nfsvers=4.1
  nfs:
    path: /srv/nfs4
    server: 10.0.0.253

```
pvc

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: nfs-client-pvc
spec:
  storageClassName: nfs-client
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 10Gi
```

deployment
 ```yaml
 apiVersion: v1
kind: Pod
metadata:
  name: nginx-pv-pod-sc
spec:
  volumes:
    - name: nginx-pv-storage
      persistentVolumeClaim:
        claimName: nfs-client-pvc
  containers:
    - name: nginx
      image: nginx
      ports:
        - containerPort: 80
          name: "nginx-server"
      volumeMounts:
        - mountPath: "/usr/share/nginx/html"
          name: nginx-pv-storage
 
 ```



### nfs provisioner
https://github.com/kubernetes-sigs/nfs-subdir-external-provisioner

```sh
helm repo add nfs-subdir-external-provisioner https://kubernetes-sigs.github.io/nfs-subdir-external-provisioner/
helm install nfs-subdir-external-provisioner nfs-subdir-external-provisioner/nfs-subdir-external-provisioner \
    --set nfs.server=x.x.x.x \
    --set nfs.path=/exported/path

```
Alternatif

https://github.com/edib/kubernetes/tree/master/yamls/nfs-provisioner


* 1 kere provisioner üretince bir daha pv üretmeye gerek kalmıyor.

* Helm ile nfs provisioner üzerinden loki stack kurulumu

```sh
helm repo add grafana https://grafana.github.io/helm-charts

helm show grafana/loki-stack > ../../helm/values.yaml
```

```yaml
#values.yaml
loki:
  enabled: true
  persistence:
    enabled: true
    storageClass: nfs-client
    size: 1G

promtail:
  enabled: true

grafana:
  enabled: true
  sidecar:
    datasources:
      enabled: true
  image:
    tag: 8.1.6

prometheus:
  enabled: true
  persistence:
    enabled: true
    storageClass: nfs-client
    size: 1G
```

```yaml
helm install loki-stack grafana/loki-stack --values values.yaml -n monitoring --create-namespace
```