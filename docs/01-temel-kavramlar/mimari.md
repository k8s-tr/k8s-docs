---
layout: default
title: Küme Mimarisi
parent: Temel Kavramlar
nav_order: 4
---

# Küme Mimarisi

## Pod-Node Bağlamı
![Cluster](../kaynaklar/nodes.png)

# Bileşen/Düzlem Bağlamı


Kontrol düzlemi, veri düzlemi ve yönetim düzlemi, bilişim kaynaklarındaki iletişim mimarisinin üç temel bileşenidir. **Kontrol düzlemi ve yönetim düzlemi**, tüm ağın taşımak için var olduğu trafiği taşıyan **veri düzlemine** hizmet eder. Yönetimsel trafik taşıyan yönetim düzlemi, kontrol düzleminin bir alt kümesi olarak kabul edilir. Veri düzlemi asıl vereceğimiz hizmetin trafiğinin aktığı düzlemdir. 

## Kontrol Düzlemi Bileşenleri

![](../kaynaklar/cluster.png)

### kube-apiserver

Kubernetes cluster mekanizmasının ortasında yer almaktadır. Master sunucumuza gelen tüm REST request'lerin yönetilmesi bu araç üzerinden gerçekleştirilir. Tüm istekler bu servis tarafından kabul edilir ve doğrulanır, ayrıca etcd veritabanına yapılan tek bağlantı bu ajanla yapılır. Sonuç olarak cluster içerisindeki ana işlem yeridir diyebiliriz. API hizmeti verir. Gelen istekler doğrular.  Kimlik doğrulama, yetkilendirme ve erişim denetimi yapar.

* Static pod olarak kurulur. 
  
```sh
/etc/kubernetes/manifests/kube-apiserver.yaml

```

### etcd

Kubernetes üzerinde gerçekleştirilen bütün konfigürasyon ve durumların tutulduğu yüksek hızlı (10000 istek/sn) high-available mod'da çalışabilen, dağıtık, tutarlı bir key-value store’dur.

* Raft algoritmasıyla lider seçimi yapar.
* Veriler bellekte durur, istenildiği zamanlar diske kalıcı olarak yazılabilir.
* HTTP protokolü kullanır.
* Tuttuğu değerler için zaman aşımları tanımlanabilmesine izin verir.
* Kendi ayarlarına /config şeklinde HTTP protokolüyle erişilebilir.

* https://etcd.io/
* https://kubernetes.io/docs/tasks/administer-cluster/configure-upgrade-etcd/



#### Key-Vaue Yapısı

* key-value çiftleri düz bir ikili anahtar düzleminde saklanır.
* Bu anahtar uzayı sözlük gibi sıralanır.
* Değerler değişmez (immutable) olarak tutulur. Varolan bir değerin değiştirilmesi istenirse eski değeri sürümleyerek korur ve yeni * değer oluşturur. 
* Eski verileri silmek, sıkıştırma (compact) işlemiyle yapılır yoksa verilerin tüm sürümleri hep kalır.


#### Dikkat edilmesi Gerekenler


* Etcd tek sayı üyelerden oluşan bir küme olarak çalıştırılır.
* Etcd'ye yeterli kaynak verilmesi gerekir. Çok fazla cpu'ya ihtiyaç duymaz, canlı sistemlerde 8GB bellek ve ortalama bir disk genel * ihtiyaçlarını karşılar.
* Kümenin performansı ve kararlılığı, ağa ve disk performansına duyarlıdır. Herhangi bir kaynak açlığı, zaman aşımına yol açarak kümenin kararsızlığına neden olabilir. Kararsız bir etcd, hiçbir liderin seçilmediğini gösterir. Bu tür koşullar altında, bir küme mevcut durumunda herhangi bir değişiklik yapamaz, bu da yeni podların oluşturulamaz.
* Etcd kümelerini kararlı tutmak, Kubernetes kümelerinin kararlılığı için kritik öneme sahiptir. Bu nedenle, garantili kaynak gereksinimleri için özel makinelerde veya yalıtılmış ortamlarda etcd kümelerini çalıştırın.

* Bağımsız docker container olarak kurulur. 

### kube-controller-manager 
Kuberneteste 2 durum vardır. Declarative olarak kubernetese söylenen ve gerçekten varolan durum. Temel olarak controller manager, kümenin durumunu API Server izleme özelliğiyle izler ve bildirildiğinde, geçerli durumu istenen duruma doğru hareket ettirmek için gerekli değişiklikleri yapar.

```sh
/etc/kubernetes/manifests/kube-controller-manager.yaml
```

### kube-scheduler

Bir pod’un hangi node üzerinde çalışacağına karar verir , etiketlere göre podları nodlara dağıtır, kubelet’i tetikler ve ilgili pod ve içindeki konteyner çalıştırılır. Kısacası yeni bir pod oluşturulması isteğine karşı API server’ı izler. 

#### Dikkate aldığı konular
* Resource requirements
* Service requirements
* Hardware/software policy constraints
* Node affinity and antiaffinity specifications
* Pod affinity and antiaffinity specifications
* Taints and tolerations
* Data locality
* Deadlines

```sh
/etc/kubernetes/manifests/kube-scheduler.yaml
```
## Düğüm Bileşenleri


### kubelet

Her bir kubernetes node’unda çalışan agent’tır. İlk işi bulunduğu node’u API Server’a Node resource olarak kayıt ederek Kubernetes tarafından görülmesini sağlamaktır. Bu işlemden sonra sürekli olarak API server’ı dinleyerek bulunduğu node’a herhangi bir Pod schedule edip edilmediğini kontrol eder. Schedule eden bir Pod varsa bulunduğu node üzerinde Pod’un içerisindeki tanımlanan container'ları çalıştırır. Buna ek olarak sürekli olarak node’da çalışan container'ların ayakta olup olmadığını kontrol ederek erişilebilir olmalarını sağlar.

* Downloading pod secrets from the API server
* Mounting volumes
* Running the pod's container (through the CRI or rkt)
* Reporting the status of the node and each pod
* Running container liveness probes


* Sistem servisi olarak çalışır.  
```sh
systemctl cat kubelet

/etc/kubernetes/kubelet.env

```

## coredns

kube-sytem nste deployment olarak kurulur.

* https://coredns.io/  
* https://kubernetes.io/docs/tasks/administer-cluster/coredns/

### kube-proxy

Service ve Endpoint objelerinin erişebilirliğini sağlamak için node üzerindeki network kurallarını ayarlar ve connection forwarding işlemini gerçekleştirir. Görevlerinden biri de Kubernetes Service’lere ve Pod'lara virtual IP atamasıdır.

* daemonset olarak oluşur. 

### container-runtime

* sistem servisi olarak kurulur. 

https://www.inovex.de/de/blog/containers-docker-containerd-nabla-kata-firecracker/
https://thenewstack.io/a-security-comparison-of-docker-cri-o-and-containerd/

### CNI 
    https://docs.projectcalico.org/reference/architecture/overview

##### calico

* kube-system altında calico-kube-controllers adında deployment olarak kurulur. 
* calico-node olarak daemonset üretir. 
* diğer alternatifleri: [cilium](https://cilium.io/), [weave](https://www.weave.works/)

```sh
# önemli dizinler
/opt/cni/bin/

```

## Ek Özellikler

# web ui
* https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/
* https://github.com/rancher/ui
* https://github.com/kubernetes/dashboard
    
# metrics-server
* https://github.com/kubernetes-sigs/metrics-server

# grafana-prometheus
* https://grafana.com/grafana/dashboards/6417-kubernetes-cluster-prometheus/
* https://github.com/kubernetes-monitoring/kubernetes-mixin

# Küme Düzeyinde Loglama 
* https://kubernetes.io/docs/concepts/cluster-administration/logging/
    
# Openshift Ek Özellikleri
* https://docs.openshift.com/container-platform/4.14/architecture/architecture.html

https://azuredays.com/2020/12/09/understanding-kubernetes-workload-objects/

https://kubernetes.io/docs/concepts/overview/components/
[Kubernetes Mimarisi](../kaynaklar/Kubernetes-mimarisi.pdf)