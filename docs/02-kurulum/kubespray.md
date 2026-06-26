---
layout: default
title:  Kubespray
parent: Kurulum
nav_order: 1
---

# Kubespray

[Resmi Sitesi](https://github.com/kubernetes-sigs/kubespray)

Kubespray, Kubernetes kurulum ve bakımını otomatikleştiren, Ansible üzerine geliştirilmiş resmi bir araçtır.

Sağladıkları:

* Otomasyon
* HA: https://github.com/kubernetes-sigs/kubespray/blob/master/docs/ha-mode.md
* Node ekleme, çıkarma ve güncelleme
* Eklenti ekleme ve çıkarma
* Farklı topolojileri destekler:
    1. Master ve etcd aynı sunucularda
    2. Etcd sunucuları ayrı (>=3, tek haneli)
    3. Master ve etcd sayıları ayarlanabilir

---

## Gereksinimler

* Ansible kontrol makinası: WSL veya bir Linux makinası olmalıdır.
* Sunucular arası ağ erişimi için [port gereksinimleri](https://kubernetes.io/docs/reference/ports-and-protocols/) karşılanmalıdır.
* Kontrol makinasında **Docker** kurulu olmalıdır.
* Hedef sunuculara SSH anahtarı ile erişim sağlanmış olmalıdır.

---

## Docker ile Kurulum (Önerilen)

Docker imajı kullanmak, sistem bağımlılıklarını yönetmekten kurtarır ve tekrarlanabilir kurulum sağlar.


### 1. Kubespray Deposunu Klonla

* Bu makinanın linux olması gerekiyor. kubespray içindeki symlinkler windowsta çalışmıyor. 

```bash
git clone https://github.com/kubernetes-sigs/kubespray.git
cd kubespray
```

### 2. Docker İmajını Çek

```bash
# Kuracağınız Kubernetes sürümüne göre kubespray imaj sürümünü seçin.
# Mevcut sürümler: https://quay.io/repository/kubespray/kubespray?tab=tags
export KUBESPRAY_VERSION=v2.29.0

export KUBESPRAY_VERSION=v2.31.0

wget https://github.com/kubernetes-sigs/kubespray/archive/refs/tags/${KUBESPRAY_VERSION}.tar.gz
tar xzf ${KUBESPRAY_VERSION}.tar.gz
cd kubespray-${KUBESPRAY_VERSION#v}

docker pull quay.io/kubespray/kubespray:${KUBESPRAY_VERSION}
```

### 3. Inventory Hazırla

```bash
# Hazır şablonu kopyala
cp -rfp inventory/sample inventory/mycluster

# Hedef sunucu IP'lerini tanımla
declare -a IPS=(<server1_ip> <server2_ip> <server3_ip>)

# hosts.yaml dosyasını oluştur (Docker container içinde çalıştır)
docker run --rm \
  -v "$(pwd)":/kubespray \
  quay.io/kubespray/kubespray:${KUBESPRAY_VERSION} \
  python3 contrib/inventory_builder/inventory.py ${IPS[@]}

# Oluşturulan dosyayı kontrol et
cat inventory/mycluster/hosts.yaml
```

> İlk 2 IP adresi otomatik olarak master ve etcd node olarak atanır.

### 4. Ayarları Gözden Geçir

```bash
# Genel ayarlar
cat inventory/mycluster/group_vars/all/all.yml

# Kubernetes küme ayarları
cat inventory/mycluster/group_vars/k8s_cluster/k8s-cluster.yml
```

#### Sık Kullanılan Ek Ayarlar

```yaml
# inventory/mycluster/group_vars/all/all.yml

# Hostname değiştirmeyi kapat
override_system_hostname: false
```

```yaml
# inventory/mycluster/group_vars/k8s_cluster/k8s-cluster.yml

enable_nodelocaldns: false 

# Calico subnet ayarları (mevcut ağınızla çakışmaması için değiştirin)
kube_service_addresses: 10.233.0.0/18
kube_pods_subnet: 10.233.64.0/18

# Sertifika otomatik yenileme
auto_renew_certificates: true
auto_renew_certificates_systemd_calendar: "Sat *-*-1,2,3,4,5,6,7 03:{{ groups['kube_control_plane'].index(inventory_hostname) }}0:00"
```

### 5. Kubernetes Kümesini Kur

SSH özel anahtarınızı ve inventory dizinini container'a mount ederek kurulum playbook'unu çalıştırın:

```bash
# SSH anahtarınızın yolu (genellikle ~/.ssh/id_rsa veya ~/.ssh/id_ed25519)
export SSH_KEY=~/.ssh/id_rsa

# root ile doğrudan erişim
docker run --rm -it \
  -v "$(pwd)":/kubespray \
  -v "${SSH_KEY}":/root/.ssh/id_rsa:ro \
  quay.io/kubespray/kubespray:${KUBESPRAY_VERSION} \
  ansible-playbook -i inventory/mycluster/hosts.yaml \
    --private-key /root/.ssh/id_rsa \
    --become --become-user=root \
    cluster.yml

docker run --rm -it --mount type=bind,source="$(pwd)"/inventory/sample,dst=/inventory \
  --mount type=bind,source="${HOME}"/.ssh/id_rsa,dst=/root/.ssh/id_rsa \
  quay.io/kubespray/kubespray:${KUBESPRAY_VERSION} bash

# docker container içinde 
ansible-playbook -i /inventory/inventory.ini --private-key /root/.ssh/id_rsa cluster.yml  

# eğer user/sadece parola varsa sudo yetkisi olmak 
ansible-playbook -i /inventory/inventory.ini -kK -b -u <user> cluster.yml  


# Sudo yetkili kullanıcı ile erişim (-kK: SSH ve sudo parolası sorar)
docker run --rm -it \
  -v "$(pwd)":/kubespray \
  -v "${SSH_KEY}":/root/.ssh/id_rsa:ro \
  quay.io/kubespray/kubespray:${KUBESPRAY_VERSION} \
  ansible-playbook -i inventory/mycluster/hosts.yaml \
    --private-key /root/.ssh/id_rsa \
    -b -u <kullanıcı> -kK \
    cluster.yml

# Ek değişkenler ve hardening ile kurulum
docker run --rm -it \
  -v "$(pwd)":/kubespray \
  -v "${SSH_KEY}":/root/.ssh/id_rsa:ro \
  quay.io/kubespray/kubespray:${KUBESPRAY_VERSION} \
  ansible-playbook -v cluster.yml \
    -i inventory/mycluster/hosts.yaml \
    --private-key /root/.ssh/id_rsa \
    --become -u <kullanıcı> -kK \
    -e "@vars.yaml" \
    -e "@hardening.yaml"
```

[Güvenlik güçlendirme ayarları](../09-G%C3%BCvenlik/kubespray-hardening.md)


## Desktop Ortamda Test (Bonus)

Kendi bilgisayarınızda test etmek için [vagrant](vagrant.md) dosyasındaki Vagrant ve VirtualBox araçlarını kullanabilirsiniz.

---

## Yeni Node Ekleme

Worker node eklemek için:

```bash
# Yeni IP'yi de dahil ederek inventory'yi güncelle
declare -a IPS=(<server1_ip> <server2_ip> <server3_ip> <yeni_node_ip>)

docker run --rm \
  -v "$(pwd)":/kubespray \
  quay.io/kubespray/kubespray:${KUBESPRAY_VERSION} \
  python3 contrib/inventory_builder/inventory.py ${IPS[@]}

# Sadece yeni node'a playbook çalıştır
docker run --rm -it \
  -v "$(pwd)":/kubespray \
  -v "${SSH_KEY}":/root/.ssh/id_rsa:ro \
  quay.io/kubespray/kubespray:${KUBESPRAY_VERSION} \
  ansible-playbook -i inventory/mycluster/hosts.yaml \
    --private-key /root/.ssh/id_rsa \
    -b -u <kullanıcı> -kK \
    scale.yml \
    --limit=<hosts.yaml_içindeki_node_adı>
```

---

## Sertifika Yenileme

Otomatik yenileme kapalıysa sertifikaları elle yenileyin:

```bash
# Sertifika sürelerini kontrol et
sudo /usr/local/bin/kubeadm certs check-expiration

# Tüm control-plane node'larında çalıştır
sudo /usr/local/bin/k8s-certs-renew.sh

# Tüm worker node'larda kubelet'i yeniden başlat
sudo systemctl restart kubelet
```

---

## [Dashboard ve Eklenti Kurulumu](dashboard.md)

**Kaynaklar:**
- [Node Ekleme ve Değiştirme](https://github.com/kubernetes-sigs/kubespray/blob/master/docs/operations/nodes.md)
- [Kubespray Docker İmajları](https://quay.io/repository/kubespray/kubespray?tab=tags)
