---
layout: default
title:  Kubespray
parent: Kurulum
nav_order: 1
---

# kubespray

[Resmi Sitesi](https://github.com/kubernetes-sigs/kubespray)

Kubespray kubernetes kurulum ve bakımlarını otomatikleştiren, ansible üzerine geliştirilmiş, resmi bir araçtır.

Sağladıkları:

* Otomasyon
* HA:  https://github.com/kubernetes-sigs/kubespray/blob/master/docs/ha-mode.md
* node ekleme ve çıkarma, güncelleme
* Eklenti ekleme, çıkarma
* farklı kurulumları destekler 
    1. masterlar ve etcdler birlikte
    2. etcd serverlar ayrı sunucularda ( >=3, tek haneli)
    3. master ve etcd sayıları ayarlanabilir. 

## Kurulum

## Desktop Ortama Kurmak (Bonus)

Kendi ortamımızda test etmek istiyorsak [vagrant](vagrant.md) dosyasındaki vagrant ve virtualbox araçlarıyla hızlıca deneyebiliriz.

---



* Ansible kontrol makinası WSL ya da bir linux makinası olmak zorundadır. 
* Sunucular arasındaki erişimler için [buraya](https://kubernetes.io/docs/reference/ports-and-protocols/) uyulmak zorundadır.

Kontrol makinasında pip3'ün kurulu olması gerekir.

```bash
# redhat grubu
sudo dnf install python311
sudo alternatives --config python
sudo alternatives --config python3

# python virtual env
VENVDIR=kubespray-venv
KUBESPRAYDIR=${kubespray dizini}/
python3 -m venv $VENVDIR
source $VENVDIR/bin/activate
cd $KUBESPRAYDIR

# ``requirements.txt``ye göre gerekenleri kurun.
pip install -U -r requirements.txt

```

* ``inventory/sample`` hazır şablonunu ``inventory/mycluster`` olarak kopyala

```
cp -rfp inventory/sample inventory/mycluster
```

* Kubernetes kuracağınız sunucuların listesini Ansible'a veriyoruz.
* Bu komut ip adreslerini ilk 2.si master ve etcd olacak şekilde nodelar olarak `inventory/mycluster/hosts.yaml` içerisine yazar. 

```
declare -a IPS=(<server1_ip> <server2_ip> <server3_ip>)
CONFIG_FILE=inventory/mycluster/hosts.yaml python3 contrib/inventory_builder/inventory.py ${IPS[@]}

```

* ``inventory/mycluster/group_vars`` klasörü altındaki bu dosyaları gözden geçirin, değiştirmek istediklerinizi değiştirin. 

```
cat inventory/mycluster/group_vars/all/all.yml
cat inventory/mycluster/group_vars/k8s_cluster/k8s-cluster.yml
```

### ek ayarlar
* hostnameleri değiştirmek istemiyorsak

```
# inventory/mycluster/group_vars/all/all.yml dosyasının içine aşağıdakini ekliyoruz.

override_system_hostname: false
```

* calico subnetlerini değiştirmek istiyorsak

```
# mycluster/group_vars/k8s_cluster/k8s-cluster.yml
# bu değerleri uygun şekilde değiştiriyoruz. 
 76 kube_service_addresses: 10.233.0.0/18

 81 kube_pods_subnet: 10.233.64.0/18

```

* Ansible ile Kubespray'i çalıştırın ve Kubernetes kümenizi kurun. Burada sudo yetkisine sahip bir kullanıcı gerekmektedir. Eğer sunuculara parola ile erişiyorsanız ``-kK`` size erişim parolası ve sudo parolasını soracaktır.

[Güvenlik için güçlendirme](../09-G%C3%BCvenlik/kubespray-hardening.md)

```bash

# root kullanıcısı ile erişmek için
ansible-playbook -i inventory/mycluster/hosts.yaml  --become --become-user=root cluster.yml

# sudo yetkisi olan bir kullanıcı ile erişmek için
ansible-playbook -i inventory/mycluster/hosts.yaml -b cluster.yml -u <kullanıcı> -kK

# ek değişkenler ve hardening eklenmiş kurulum

ansible-playbook -v cluster.yml \
        -i inventory/mycluster/hosts.yaml \
        -become -u <kullanıcı> -kK \
        -e "@vars.yaml" \
        -e "@hardening.yaml"
```
*  Eksik bir şey yoksa yukarıdaki IPS tanımında verilen sunuculara kubernetes kümesi kurulacaktır. 

## Yeni nod ekleme

```
declare -a IPS=(<server1_ip> <server2_ip> <server3_ip> <yeni_nod_ip>)
CONFIG_FILE=inventory/mycluster/hosts.yaml python3 contrib/inventory_builder/inventory.py ${IPS[@]}

ansible-playbook -i inventory/mycluster/hosts.yaml -b cluster.yml -u <kullanıcı> -kK --limit=kube_control_plane
```
## [Dashboard ve yeni eklenti kurma](dashboard.md)

* Kaynaklar
[Node Ekleme, Değiştirme](https://github.com/kubernetes-sigs/kubespray/blob/master/docs/nodes.md)
