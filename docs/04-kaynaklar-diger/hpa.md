---
layout: default
title: Yatay Pod Ölçekleme
nav_order: 4
parent: Diğer Kaynaklar
---

# hpa

Kubernetes'te HPA (Horizontal Pod Autoscaler), podların otomatik olarak ölçeklenmesini sağlayan bir bileşendir. HPA, belirlediğiniz metrikler (örn. CPU kullanımı, bellek kullanımı) üzerinden belirli sınırlara ulaşıldığında pod sayısını artırabilir veya azaltabilir.

Örneğin, bir Deployment'ınızın CPU kullanımı %80'in üzerine çıktığında otomatik olarak daha fazla pod eklenmesini ve %50'nin altına düştüğünde pod sayısının azaltılmasını istiyorsanız, HPA kullanarak bunu gerçekleştirebilirsiniz.

HPA ile `Deployment`'ı birlikte kullanmak için örnek bir manifest:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: example-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: example-app
  template:
    metadata:
      labels:
        app: example-app
    spec:
      containers:
      - name: example-container
        image: nginx:latest
        resources:
          requests:
            cpu: "250m"
          limits:
            cpu: "500m"
---
apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler
metadata:
  name: example-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: example-deployment
  minReplicas: 1
  maxReplicas: 10
  targetCPUUtilizationPercentage: 80
```

Bu manifestte:

1. Bir `Deployment` tanımlandı. Başlangıçta 3 replica ile başlar.
2. Her pod için CPU talepleri (`requests`) ve sınırlamaları (`limits`) tanımlandı. Bu, HPA'nın ölçeklendirme kararlarını alırken kullanılacak metrikleri doğru bir şekilde değerlendirmesi için önemlidir.
3. `HorizontalPodAutoscaler` tanımlandı. Bu, önceden tanımladığımız `Deployment`'ı hedef alır.
4. Pod sayısının otomatik ölçeklendirme için minimum ve maksimum değerleri tanımlandı. Bu örnekte, en az 1, en fazla 10 pod çalıştırılabilir.
5. CPU kullanımının %80'in üzerine çıktığında HPA'nın yeni pod'lar başlatmasını istiyoruz.

Bu manifesti `kubectl` aracılığıyla uygulayarak bir `Deployment` ve onunla ilişkilendirilmiş bir `HPA` oluşturabilirsiniz:

```bash
kubectl apply -f <manifest-dosya-adı.yaml>
```
