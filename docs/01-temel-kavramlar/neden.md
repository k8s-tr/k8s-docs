---
layout: default
title: "Neden Konteynerlar?"
parent: Temel Kavramlar
nav_order: 1
---

# Neden Konteynerlere ihtiyaç duyarız?

* **Çevik uygulama oluşturma ve dağıtma:** Uygulama sanal makinesi imaj kullanımına kıyasla kolay ve verimli uygulama imajı oluşturma kolaylığı.
* **Sürekli geliştirme, entegrasyon ve dağıtım:** Güvenilir ve sık sık konteyner görüntü oluşturma ve dağıtımı sağlarken, hızlı ve kolay geri almaları da destekler (imaj değişmezliği nedeniyle).
* **Geliştirici ve Operasyon görevlerinin ayrılması:** Uygulama imajlarını  build / release zamanında oluşturulur ve böylece uygulamanın değişmezliğini altyapıdan ayırmış oluruz.
* **Gözlemlenebilirlik:** Bilgi ve metrikler, yalnızca işletim sistemi düzeyinde değil, uygulamanın sağlığı ve diğer sinyalleri de yansıtır.
* **Geliştirme, test ve canlı ortamlarda çevresel tutarlılık:** Dizüstü bilgisayarda da bulutta olduğu gibi çalışır.
Bulut ve işletim sistemi dağıtım taşınabilirliği: Ubuntu, RHEL, CoreOS, şirket içi, büyük genel bulutlarda ve başka herhangi bir yerde çalışır ve hep aynı şekilde çalışır.
* **Uygulama merkezli yönetim:** Mantıksal kaynakları kullanarak işletim sisteminden soyutlayarak sanal donanımda uygulama çalıştırmayı sağlar.
* **Gevşek bağlanmış, dağıtılmış, esnek, serbest bırakılmış mikro hizmetler:** Uygulamalar daha küçük, bağımsız parçalara bölünür ve dinamik olarak dağıtılabilir ve yönetilebilir.
* **Kaynak yalıtımı:** Öngörülebilir uygulama performansı.
* **Kaynak kullanımı:** Yüksek verimlilik ve yoğunluk.


# Neden Kubernetes Kullanırız?

* **Servis keşfi ve yük dengeleme:** Kubernetes, bir konteyneri DNS adını veya kendi IP adreslerini kullanarak ağda sunabilir. Bir konteynere gelen trafik yüksekse, Kubernetes tüm uygulamanın kararlı olması için yükü dengeler  ve ağ trafiğini dağıtabilir.
Depolama yönetimi: Kubernetes, yerel diskler, genel bulut sağlayıcıları ve daha fazlası gibi istediğiniz bir depolama sistemini otomatik olarak alttaki işletim sistemine monte etmenizi sağlar.
* **Otomatik dağıtımlar ve geri almalar:** Kubernetes kullanarak konuşlandırılan konteynerleriniz için her zaman sabit olmasını istediğiniz durumu önceden tanımlayabilirsiniz. K8s, bu sayede gerçek durumu kontrollü bir hızda istenen duruma değiştirebilir. Örneğin, dağıtımınız için yeni konteynerler oluşturmak, mevcut konteynerleri kaldırmak ve tüm kaynaklarını yeni konteynerlere almak için Kubernetes'i otomatikleştirebilirsiniz.
* **Otomatik paketleme:** Kubernetes'e, konteynerdeki görevleri çalıştırmak için kullanabileceği bir node kümesi sağlarsınız. Kubernetes'e her bir konteyner için ne kadar CPU ve bellek (RAM) gerektiğini söylersiniz. Kubernetes, kaynaklarınızdan en iyi şekilde yararlanmak için konteynerlerinizi nodelarınıza göre ayarlar.
* **Kendi kendine iyileştirme:** Kubernetes, başarısız olan konteynerleri yeniden başlatır, onları değiştirir, kullanıcı tanımlı sağlık kontrolünüze yanıt vermeyenleri öldürür ve hizmet vermeye hazır oluncaya kadar istemci trafiğine açmaz.
* **Gizli bilgi ve konfigürasyon yönetimi:** Kubernetes, parolalar, OAuth anahtarları ve SSH anahtarları gibi hassas bilgileri depolamanızı ve yönetmenizi sağlar. Konteyner imajlarınızı yeniden oluşturmadan ve yığın yapılandırmanızdaki gizli bilgileri açığa çıkarmadan yeni gizli bilgileri ve uygulama yapılandırmasını dağıtabilir ve güncelleyebilirsiniz.


Bize Sağladıkları
 
* Sağladıkları
    - Service Discovery
    - Resillience (Cluster and resource level)
    - Load balancing
    - healthchecks
    - cluster authorization
    - resource management
    - autoscaling (horizontal & vertical)
    - Observability (Cluster and resurce level) (prometheus, grafana)
* Service mesh (istio, linkerd)
  - Trafik Management
    - Request Routing
    - Traffic Shifting
    - Mirroring
    - ingress gateway
    - egress gateway
  - Security
    - mTLS, Certicficate Management
    - Authentication
    - Peer Authentication
    - Request Authentication
  - Service Resilliency
    - Timeouts
    - Retries
    - Circuit breaker
    - Error İnjection (Ağ sağlamlığı)
  - Observability (service level)
    -  Metrics
    -  Logs and tracing
 -  Stateful Applications
    -  persistent storage
    -  statefulsets
 -  Logging