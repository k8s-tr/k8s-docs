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


alias kubectl="microk8s kubectl"
alias k="kubectl"

alias m="microk8s"

```

#### Kullanılabilir ek hizmetler. 

```
m status
 
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

####  komutlar

```
microk8s

Available subcommands are:
	add-node # 
	cilium # Another CNI
	config
	ctr # containerd client
	dashboard-proxy
	dbctl # backup and restore the Kubernetes datastore.
	disable 
	enable
	helm3
	helm
	istioctl # service mesh
	join
	juju
	kubectl
	leave # The node will depart from the cluster it is in.
	linkerd # service mesh
	refresh-certs #   Replace the CA certificates with the ca.crt and ca.key found in CA_DIR.
	remove-node
	reset
	start
	status
	stop 
	inspect # This script will inspect your microk8s installation. It will report any issue it finds,
and create a tarball of logs and traces which can be attached to an issue filed against
the microk8s project.


# istediğimiz servisleri etkinleştiriyoruz

# registry: kendi registryniz olsun isterseniz. 
microk8s enable dashboard dns registry


# dashboarda nodeport üzerinden erişmek için
kubectl -n kube-system patch svc kubernetes-dashboard --type='json' -p '[{"op":"replace","path":"/spec/type","value":"NodePort"}]'

# dashboard nodeportunu öğreniyoruz.

k describe svc kubernetes-dashboard -n kube-system | grep NodePort


# dashboarda erişim için aşağıdaki komuttan çıkan token'ı kullanıyoruz.
{% raw %}
kubectl -n kube-system get secret $(kubectl -n kube-system get sa default -o jsonpath="{.secrets[0].name}") -o go-template="{{ .data.token | base64decode }}" && echo
{% endraw %}
```


#### yeni node eklemek

```

microk8s add-node

# çıkan komutu diğer nodlarda microk8s kurduktan sonra çalıştırın



```