---
layout: default
parent: Görevler
nav_order: 3
title: İç Yük Dengeleme
---

# İç Yük Dengeleme

* nginx.conf configmapte saklanarak tcp load balance özelliği kullanılabilir.

```yaml

apiVersion: v1
kind: ConfigMap
metadata:
  name: nginx-lb-conf
data:
  nginx.conf: |
    user nginx;
    worker_processes  1;
    events {
      worker_connections  50;
    }

    stream {
        upstream myservis_lb {
        server $host_ip1:9200;
        server $host_ip2:9200;
        server $host_ip3:9200;
      }
          server {
            listen     9200;
            proxy_pass myservis_lb;
        }
    }

---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-lb-tcp
spec:
  selector:
    matchLabels:
      app: nginx-lb-tcp
  replicas: 1
  template:
    metadata:
      labels:
        app: nginx-lb-tcp
    spec:
      containers:
      - name: nginx
        image: nginx
        ports:
        - containerPort: 9200
        volumeMounts:
            - name: nginx-lb-conf
              mountPath: /etc/nginx/nginx.conf
              subPath: nginx.conf
              readOnly: true
      volumes:
      - name: nginx-lb-conf
        configMap:
          name: nginx-lb-conf
          items:
            - key: nginx.conf
              path: nginx.conf

---
apiVersion: v1
kind: Service
metadata:
  name: nginx
spec:
  ports:
  - port: 9200
    protocol: TCP
    targetPort: 9200
  selector:
    app: nginx-lb-tcp

```