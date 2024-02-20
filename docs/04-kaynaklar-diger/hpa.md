---
layout: default
title: Yatay Pod Ölçekleme
nav_order: 4
parent: Diğer Kaynaklar
---

## hpa

Kubernetes'te HPA (Horizontal Pod Autoscaler), podların otomatik olarak ölçeklenmesini sağlayan bir bileşendir. HPA, belirlediğiniz metrikler (örn. CPU kullanımı, bellek kullanımı) üzerinden belirli sınırlara ulaşıldığında pod sayısını artırabilir veya azaltabilir.

Örneğin, bir Deployment'ınızın CPU kullanımı %80'in üzerine çıktığında otomatik olarak daha fazla pod eklenmesini ve %50'nin altına düştüğünde pod sayısının azaltılmasını istiyorsanız, HPA kullanarak bunu gerçekleştirebilirsiniz.

HPA ile `Deployment`'ı birlikte kullanmak için örnek bir manifest:

https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale-walkthrough/

Bu manifestte:

1. Bir `Deployment` tanımlandı. Başlangıçta 3 replica ile başlar.
2. Her pod için CPU talepleri (`requests`) ve sınırlamaları (`limits`) tanımlandı. Bu, HPA'nın ölçeklendirme kararlarını alırken kullanılacak metrikleri doğru bir şekilde değerlendirmesi için önemlidir.
3. `HorizontalPodAutoscaler` tanımlandı. Bu, önceden tanımladığımız `Deployment`'ı hedef alır.
4. Pod sayısının otomatik ölçeklendirme için minimum ve maksimum değerleri tanımlandı. Bu örnekte, en az 1, en fazla 10 pod çalıştırılabilir.
5. CPU kullanımının %80'in üzerine çıktığında HPA'nın yeni pod'lar başlatmasını istiyoruz.

```yaml

apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: php-apache
spec:
  behavior:
    scaleDown:
      policies:
      - periodSeconds: 15
        type: Pods
        value: 1
      - periodSeconds: 15
        type: Percent
        value: 80
      selectPolicy: Max
      stabilizationWindowSeconds: 0
    scaleUp:
      policies:
      - periodSeconds: 15
        type: Pods
        value: 1
      selectPolicy: Min
      stabilizationWindowSeconds: 0
  maxReplicas: 10
  metrics:
  - resource:
      name: cpu
      target:
        averageUtilization: 20
        type: Utilization
    type: Resource
  minReplicas: 2
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: php-apache

```

## Başkaları

https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale-walkthrough/#autoscaling-on-metrics-not-related-to-kubernetes-objects