---
title:  Service Mesh 
layout: default
parent: Ek Araçlar
---

# Service Mesh 
* **Microservis Bağlantısı:** Mikroservislerin birbirleriyle konuşmasını ve bağlantıların akıllı bir şekilde yönlendirilmesini sağlar.
* **Güvenlik:** Merkezi bir yapılandırmayla servisler arası güvenli iletişim ve merkezi erişim ve sınırlandırma politikaları uygulanabilir.
* **İzleme:** Servis mesh kurulumlarına doğrudan izleme araçları gömülü ve kullanıma hazır olarak gelir. Bu sayede kim kimle ne kadar konuşuyor, gecikme nerede var vb. detaylı trafik bilgileri tüm servisler için görünür olur.
* **Ayrıştırma:** Tüm bu işlemler, koddan bağımsız olarak yapılabilir. Kodda fonksiyonel gereksinimler dışında bir bilgi bulunmaz. 
* **Esnek Kurulumlar:** Hizmet kesintilerine gerek kalmadan yeni sürümleri, sınırlı, kontrollü bir şekilde test edip, sürüm değişikliği yapabiliriz.

## Istio

Istio, mevcut dağıtılmış uygulamalar üzerinde şeffaf bir şekilde katman oluşturan açık kaynaklı bir hizmet ağıdır (service mesh). Istio, çok az veya hiç kodu değişikliği olmadan yük dengeleme, hizmetten hizmete kimlik doğrulama ve izleme sağlar.

### Neler Sağlar?

* TLS şifrelemesi, kimlik doğrulama ve yetkilendirme ile bir kümede, hizmetten hizmete güvenli iletişim
* HTTP, gRPC, WebSocket ve TCP trafiği için otomatik yük dengeleme
* Zengin yönlendirme kuralları, yeniden denemeler, yük devretmeler ve hata yerleştirme ile trafik davranışının ayrıntılı kontrolü
* Erişim kontrollerini, hız sınırlarını ve kotaları destekleyen, takılabilir bir politika katmanı ve yapılandırma API'si
* Küme girişi ve çıkışı dahil olmak üzere bir küme içindeki tüm trafik için otomatik ölçümler, günlükler ve izlemeler

### İstio Kaynakları

#### Gateway

Istio'nun ingress denetleyicisidir. 
#### VirtualService

ingress tanımıdır.
#### Destination Rules

Yönlendirme ve iş kuralı tanımları



