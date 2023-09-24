--- 
title: Daemonsets
parent: Diğer Kaynaklar
nav_order: 4
--- 
# Daemonsets

Kubernetes'te, DaemonSet, belirli bir tür pod'u (container'lar grubu) her bir Node'da çalıştırmak için kullanılan bir nesne türüdür. Diğer bir deyişle, Kubernetes kümesindeki her Node'a bir DaemonSet tarafından yönetilen bir pod yerleştirilir.

DaemonSet'in bazı yaygın kullanım durumları şunlardır:

1. **Log ve izleme ajanları**: Örneğin, her nod'ta çalışan ve nod'daki tüm container'ların loglarını toplayan bir log toplayıcı ajanı kurmak isteyebilirsiniz.
  
2. **Node izleme ve performans analizi**: Node'un kaynak kullanımını, performansını izlemek için araçları her node'da çalıştırmak.

3. **Ağ yapılandırma ve yönetimi**: Örneğin, ağ kurallarını ayarlamak veya SDN (Software Defined Networking) çözümleri için gereken ajanları çalıştırmak.

DaemonSet oluşturulduğunda, Kubernetes otomatik olarak seçilen her Node için bir pod oluşturur. Yeni bir Node eklediğinizde, bu Node için de otomatik olarak bir pod oluşturulur. Node'u kümesten çıkarırsanız, Kubernetes bu Node için olan pod'u otomatik olarak siler.

DaemonSet'in çalışma şekli, yüksek seviyede şu adımlardan oluşur:

1. DaemonSet tanımı oluşturulur.
2. DaemonSet Kubernetes API'sine gönderilir.
3. DaemonSet denetleyicisi, Kubernetes kümesindeki her Node için bir pod oluşturur.
4. Eğer bir Node kümeye eklenirse, DaemonSet yeni bir pod oluşturur. Eğer bir Node kümeden çıkarılırsa, DaemonSet pod'u siler.


```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: example-daemonset
  labels:
    app: example-app
spec:
  selector:
    matchLabels:
      name: example-daemonset-pod
  template:
    metadata:
      labels:
        name: example-daemonset-pod
    spec:
      containers:
      - name: example-container
        image: nginx:latest
```

Bu manifestte şu bilgiler bulunmaktadır:

- `apiVersion`: Kullandığımız Kubernetes API'nin versiyonunu belirtir. `DaemonSet` için `apps/v1` sıklıkla kullanılır.
- `kind`: Oluşturmak istediğimiz kaynağın türünü belirtir. Bu örnekte, bir `DaemonSet` oluşturuyoruz.
- `metadata`: Kaynağın meta verilerini belirtir. Bu örnekte kaynağa `example-daemonset` adını verdik.
- `spec`: Kaynağın özelliklerini belirtir.
  - `selector`: Hangi podların bu DaemonSet'e ait olduğunu belirten etiketleri seçer.
  - `template`: Yeni pod'ların nasıl oluşturulacağını belirten bir şablon.
    - `metadata`: Pod'un meta verileri.
    - `spec`: Pod özellikleri.
      - `containers`: Pod içerisinde çalışacak olan container'lar listesi. Bu örnekte sadece bir container bulunuyor ve bu container'ın adı `example-container`, kullanılan imaj ise `example-image:latest`.

Bu manifesti `kubectl` aracılığıyla uygulayarak bir DaemonSet oluşturabilirsiniz:

```bash
kubectl apply -f <manifest-dosya-adı.yaml>
```
