---
layout: default
title: Etiketler
parent: Temel Kavramlar
nav_order: 5
---

##  Labels
 
Kubernetes'de, bir nesneyi tanımlamak veya gruplandırmak için Labels adında bir yapı kullanılır. Label, bir nesne üzerinde tanımlanan anahtar-değer çiftidir ve bir nesne üzerinde birden fazla Label kullanılabilir.

Label'lar, YAML dosyaları aracılığıyla yapılandırılır ve nesnelerin etiketlenmesine, filtrelenmesine, seçilmesine ve gruplanmasına olanak tanır. Örneğin, bir Deployment nesnesi için birden fazla Label tanımlayabilirsiniz ve bu Label'ları kullanarak farklı Deployment nesnelerini gruplandırabilirsiniz.

Ayrıca, Label'lar, Kubernetes'de seçiciler olarak da kullanılabilir. Örneğin, bir Service nesnesi, belirli bir Label'ı olan Pod'ları hedefleyebilir. Bu şekilde, Service nesnesi, belirli bir Label'ı olan tüm Pod'ları hedefleyebilir ve bu Pod'larla iletişim kurabilir.

Label'lar, Kubernetes'te nesnelerin yönetimini kolaylaştırır ve nesnelerin gruplanması ve filtrelenmesi için güçlü bir araçtır. Label'lar, nesneler arasındaki ilişkileri ve bağımlılıkları da belirlemeye yardımcı olur ve Kubernetes'de karmaşık uygulama ortamlarının yönetimini kolaylaştırır.


```
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
  labels:
    app: my-app
    env: production
spec:
  containers:
  - name: my-container
    image: nginx

```
