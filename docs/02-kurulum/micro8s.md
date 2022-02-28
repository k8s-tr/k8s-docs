---
layout: default
title:  MicroK8s
parent: Kurulum
nav_order: 2
---

# microk8s

[Resmi Sitesi](https://microk8s.io/)

Canonical firmasının Kubernetes dağıtımıdır. 

##  linux için
```

 sudo snap install microk8s --classic
 
# kurulmasını bekliyoruz.
microk8s status --wait-ready
 
# istediğimiz servisleri etkinleştiriyoruz
microk8s enable dashboard dns registry istio
 
 
#kubectl
 
cd $HOME
mkdir .kube
cd .kube
microk8s config > config
 
snap install kubectl --classic

```


```
microk8s enable --help
microk8s disable <modul_adı>

```

```
# dev ortamında token vb uğraşmamak doğrudan giriş yapmak için
kubectl -n kube-system edit deployment/kubernetes-dashboard
 
# bu satırları
      containers:
      - args:
        - --auto-generate-certificates
        - --namespace=kube-system
 
# bu şekilde değiştirin.
      containers:
      - args:
        - --auto-generate-certificates
        - --namespace=kube-system
        - --enable-skip-login
 
 
# k dashboardu aktif etmek için
microk8s dashboard-proxy

```

#### Kullanılabilir ek hizmetler. 

```
microk8s status --wait-ready
 
microk8s is running
high-availability: no
  datastore master nodes: 127.0.0.1:19001
  datastore standby nodes: none
addons:
  enabled:
    ha-cluster           # Configure high availability on the current node
  disabled:
    ambassador           # Ambassador API Gateway and Ingress
    cilium               # SDN, fast with full network policy
    dashboard            # The Kubernetes dashboard
    dns                  # CoreDNS
    fluentd              # Elasticsearch-Fluentd-Kibana logging and monitoring
    gpu                  # Automatic enablement of Nvidia CUDA
    helm                 # Helm 2 - the package manager for Kubernetes
    helm3                # Helm 3 - Kubernetes package manager
    host-access          # Allow Pods connecting to Host services smoothly
    ingress              # Ingress controller for external access
    istio                # Core Istio service mesh services
    jaeger               # Kubernetes Jaeger operator with its simple config
    keda                 # Kubernetes-based Event Driven Autoscaling
    knative              # The Knative framework on Kubernetes.
    kubeflow             # Kubeflow for easy ML deployments
    linkerd              # Linkerd is a service mesh for Kubernetes and other frameworks
    metallb              # Loadbalancer for your Kubernetes cluster
    metrics-server       # K8s Metrics Server for API access to service metrics
    multus               # Multus CNI enables attaching multiple network interfaces to pods
    openebs              # OpenEBS is the open-source storage solution for Kubernetes
    openfaas             # openfaas serverless framework
    portainer            # Portainer UI for your Kubernetes cluster
    prometheus           # Prometheus operator for monitoring and logging
    rbac                 # Role-Based Access Control for authorisation
    registry             # Private image registry exposed on localhost:32000
    storage              # Storage class; allocates storage from host directory
    traefik              # traefik Ingress controller for external access
```

```
# istediğiniz zaman kapatıp açmak için
microk8s stop
microk8s start
```

### yeni node eklemek

```sh

microk8s add-node

# çıkan komutu diğer nodlarda çalıştırın


```