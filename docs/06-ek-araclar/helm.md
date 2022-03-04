---
title:  Helm
layout: default
parent: Ek Araçlar
---

# helm

Bir k8s uygulama kümesinin (deployment, service, configmap, secrets, ingress ... birlikte) kolayca yönetilebilmesi ve bir bütün olarak taşınabilmesi için üretilmiş bir yapılandırma paket yönetimidir. 


```sh

# Script olarak kurulum, istenirse dağıtımların depolarından da kurulabilir. 
curl https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 | bash

helm version --short

```


```console

helm repo add stable https://kubernetes-charts.storage.googleapis.com/

# helm aktif durumunu çevresel değişken olarak saklar. 
helm env

```

Helm komutu varsayılan olarak kubectl yapılandırmasını kullanır.

```
# helm repoları görelim.
helm repo list

# redis hub sunan helm chars listesi
helm search hub redis

# redis sunan repositoryler. 
helm search repo redis

# redis chart tanımı
helm show chart stable/redis

# redis readme dosyası
helm show readme stable/redis

helm install <kurulum_adi> stable/postgresql

```



Helm ile kendi chartlarımızı oluşturmak

Bir chart, values.yaml dosyası içerisinde tanımlanmış değerler için bir templateler bütünüdür. Bu dosyaya değerler atanarak templateler güncellenir. 

```
helm create ilk-app

```

Bunun sonucunda aşağıdaki dizin oluşur ve tüm olası template alanları oluşturulur.

```
tree ilk-app
ilk-app
├── charts
├── Chart.yaml
├── templates
│   ├── deployment.yaml
│   ├── _helpers.tpl
│   ├── hpa.yaml
│   ├── ingress.yaml
│   ├── NOTES.txt
│   ├── serviceaccount.yaml
│   ├── service.yaml
│   └── tests
│       └── test-connection.yaml
└── values.yaml

3 directories, 10 files
```

```
cat ilk-app/Chart.yaml | grep -v "^#"
apiVersion: v2
name: ilk-app
description: A Helm chart for Kubernetes
type: application
version: 0.1.0
appVersion: 1.16.0

```

```yaml
{% raw %}
 spec: 
  replicas: {{ .Values.replicaCount }}
{% endraw %}

#values.yaml içerisinde tanımlanır.

replicaCount: 3
```

Ayrıca komut satırın bu değer aşağıdaki gibi de girilebilir. 

```yaml

... --set replicaCount=3

```

values.yaml dosyasında neler var görmek için

```sh
#helm char dizininde
helm inspect values .
```


```
# Birden çok values dosyası kullanabilirsiniz. Sonra gelen önceliklidir. 
helm install -f myvalues.yaml -f override.yaml  myredis ./redis

```

### Kaynaklar
* https://helm.sh/
* https://helm.sh/docs/chart_best_practices/
* https://www.youtube.com/watch?v=3GPpm2nZb2s
