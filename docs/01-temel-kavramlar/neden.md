---
layout: default
title: "Neden Konteynerlar?"
parent: Temel Kavramlar
nav_order: 1
---

## Konteyner Nedir?

![vm-containers karşılaştırması](../kaynaklar/containers-vs-virtual-machines.jpg)

* Daha küçük
* Oluşturması daha kısa
* Kurması daha kısa
* Her yerde aynı ayar
* Hızla çoğaltılabilir
* Bağımsız 
  * ayarlanabilir
  * izlenebilir

## Konteyner Tarihi

* Chroot: Unix in 1979.
* FreeBSD Jails: 2000
* Linux Containers (LXC): 2008
* Docker: 2013
* Kubernetes: 2014
* Openshift: 2011, 2015

## Neden Konteynerlere ihtiyaç duyarız?

* **Çevik uygulama oluşturma ve dağıtma:** Uygulama sanal makinesi imaj kullanımına kıyasla kolay ve verimli uygulama imajı oluşturma kolaylığı.
* **Sürekli geliştirme, entegrasyon ve dağıtım:** Güvenilir ve sık sık konteyner görüntü oluşturma ve dağıtımı sağlarken, hızlı ve kolay geri almaları da destekler (imaj değişmezliği nedeniyle).
* **Uygulamanın Değişmezliği:** Uygulama imajlarını  build / release zamanında oluşturulur ve böylece uygulamanın değişmezliğini altyapıdan ayırmış oluruz.
  * Geliştirme, test ve canlı ortamlarda çevresel tutarlılık
* **Gözlemlenebilirlik:** Bilgi ve metrikler, yalnızca işletim sistemi düzeyinde değil, uygulamanın sağlığı ve diğer sinyalleri de yansıtır.
* **Uygulama merkezli yönetim:** Mantıksal kaynakları kullanarak işletim sisteminden soyutlayarak sanal donanımda uygulama çalıştırmayı sağlar.
* **Mikro hizmetler:** Uygulamalar daha küçük, bağımsız parçalara bölünür ve dinamik olarak dağıtılabilir ve yönetilebilir.
* **Kaynak yalıtımı:** Öngörülebilir uygulama performansı.
* **Kaynak kullanımı:** Yüksek verimlilik ve yoğunluk.

## Neden Kubernetes Kullanırız?

* **Servis keşfi ve yük dengeleme:**
  * Bir konteyneri DNS adını veya kendi IP adreslerini kullanarak ağda sunabilir. 
  * Bir konteynere gelen trafik yüksekse, Kubernetes tüm uygulamanın kararlı olması için yükü dengeler  ve ağ trafiğini dağıtabilir.
* **Depolama yönetimi:** 
  * Yerel diskler, genel bulut sağlayıcıları ve daha fazlası gibi istediğiniz bir depolama sistemini otomatik olarak alttaki işletim sistemine monte etmenizi sağlar.
* **Otomatik dağıtımlar ve geri almalar:**
  * Uygulamayı kod olarak tanımlamak. Kaç tane olacak, hangi diske bağlanacak. kim kimle hangi isimle konuşacak vb. yaml olarak.
* **Otomatik yerleştirme:** K
  * Kullanabileceği bir node kümesi sağla. 
  * Her bir konteyner için ne kadar CPU ve bellek (RAM) gerektiğini söyle
  * Kaynaklarınızdan en iyi şekilde yararlandırır.
* **Kendi kendine iyileştirme:** Başarısız olan konteynerleri yeniden başlatır, onları değiştirir, kullanıcı tanımlı sağlık kontrolünüze yanıt vermeyenleri öldürür ve hizmet vermeye hazır oluncaya kadar istemci trafiğine açmaz.
* **Gizli bilgi ve konfigürasyon yönetimi:** 
  * Kubernetes, parolalar, OAuth anahtarları ve SSH anahtarları gibi hassas bilgileri depolamanızı ve yönetmenizi sağlar. 
  * Konteyner imajlarınızı yeniden oluşturmadan ve yığın yapılandırmanızdaki gizli bilgileri açığa çıkarmadan yeni gizli bilgileri ve uygulama yapılandırmasını dağıtabilir ve güncelleyebilirsiniz.
* **Genişletilebilir:** Openshift, özelleştirilmiş yetenekleri entegre etme.
  * Bir API ile çalışır
  * Service mesh araçları
  * operatorler
  * CRDs

### Bize Sağladıkları
 
* Sağladıkları
  * Service Discovery
  * Resillience (Cluster and resource level)
  * Load balancing
  * Health Checks
  * cluster Authorization
  * Resource Management
  * Autoscaling (horizontal & vertical)
  * Observability (Cluster and resurce level) (prometheus, grafana)
* Service mesh (istio, linkerd)
  *Trafik Management
    *Request Routing
    *Traffic Shifting
    *Mirroring
    *ingress gateway
    *egress gateway
  * Security
    * mTLS, Certicficate Management
    * Authentication
    * Peer Authentication
    * Request Authentication
  * Service Resilliency
    * Timeouts
    * Retries
    * Circuit breaker
    * Error İnjection (Ağ sağlamlığı)
  * Observability (service level)
    * Metrics
    * Logs and tracing
  * Stateful Applications
    * persistent storage
    * statefulsets
 * Logging