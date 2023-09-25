
---
layout: default
title:  kubeadm
parent: Kurulum
nav_order: 4
---

# kubeadm

##  tüm nodlarda yapılacaklar
```bash
sudo swapoff -a
(crontab -l 2>/dev/null; echo "@reboot /sbin/swapoff -a") | crontab - || true

```
 
```bash

cat <<EOF | sudo tee /etc/modules-load.d/crio.conf
overlay
br_netfilter
EOF

# Set up required sysctl params, these persist across reboots.
cat <<EOF | sudo tee /etc/sysctl.d/99-kubernetes-cri.conf
net.bridge.bridge-nf-call-iptables  = 1
net.ipv4.ip_forward                 = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv6.conf.all.disable_ipv6      = 1
net.ipv6.conf.default.disable_ipv6  = 0
net.ipv6.conf.tun0.disable_ipv6     = 0
EOF

sudo sysctl --system


```

hemen aktif etmek için (overlay network için)

```bash
sudo modprobe overlay
sudo modprobe br_netfilter

```

## crio

repoya ekle
```bash

OS="xUbuntu_22.04"

VERSION="1.27"

cat <<EOF | sudo tee /etc/apt/sources.list.d/devel:kubic:libcontainers:stable.list
deb https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/$OS/ /
EOF
cat <<EOF | sudo tee /etc/apt/sources.list.d/devel:kubic:libcontainers:stable:cri-o:$VERSION.list
deb http://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable:/cri-o:/$VERSION/$OS/ /
EOF


```

gpg ok

```bash
curl -L https://download.opensuse.org/repositories/devel:kubic:libcontainers:stable:cri-o:$VERSION/$OS/Release.key | sudo apt-key --keyring /etc/apt/trusted.gpg.d/libcontainers.gpg add -
curl -L https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/$OS/Release.key | sudo apt-key --keyring /etc/apt/trusted.gpg.d/libcontainers.gpg add -


```
cri enable

```bash
sudo apt-get update
sudo apt-get install cri-o cri-o-runc cri-tools -y

sudo systemctl daemon-reload
sudo systemctl enable crio --now

```

detay

https://cri-o.io/ 


cri-o registry ayarları

https://github.com/cri-o/cri-o/blob/main/tutorials/kubeadm.md 

Kubernetes package repositories kısmı

https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/#dpkg-k8s-package-repo


```bash
NODENAME=$(hostname -s)
POD_CIDR="192.168.0.0/20"

sudo kubeadm init --apiserver-advertise-address=$IPADDR  --apiserver-cert-extra-sans=$IPADDR --pod-network-cidr=$POD_CIDR --node-name $NODENAME --ignore-preflight-errors Swap

```

## calico 

```bash
kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.26.1/manifests/calico.yaml

```

```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

```

## bir node remove etmek

```bash
kubectl drain <nodename> --ignore-daemonsets

```

```bash
kubectl delete node <nodename>

```
###  Kaynaklar

https://www.mirantis.com/blog/how-install-kubernetes-kubeadm/ (docker ile kurulumu)

 