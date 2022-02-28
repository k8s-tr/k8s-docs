---
layout: default
title: Ana Sayfa
nav_order: 0
---


# Kubernetes Eğitimi



## Önkoşullar

* [algoritmalar ve veri yapıları](https://www.youtube.com/watch?v=r3uOBb3BM-0&list=PLh9ECzBB8tJN9bckI6FbWB03HkmogKrFT)
* [Algoritma Analizi ve Big-O](https://www.youtube.com/watch?v=3bhBo9YCTpo&list=PLh9ECzBB8tJPTWIUbZjHZMMGuZcpHUv5h)
* [programlamaya giriş](https://www.youtube.com/playlist?list=PLh9ECzBB8tJNzJqD64MAS0SK5IeNCKCzY)
* [işletim sistemleri](https://www.youtube.com/watch?v=r2q_XRKrqHQ&list=PLh9ECzBB8tJO9eiwfQbcA2ThMbUSkbOWf)
* [linux](https://www.youtube.com/watch?v=_Kc9ImFpqro&list=PLh9ECzBB8tJOnxXrUTOqXfurKOZkN4mEY)
  * Text Editors
  * User Environment 
  * Manipulating Text
  * Awk and Sed commands
  * Network Operations
  * The Bash Shell and Basic Scripting
  * Local Security Principles
  * Common Applications
  * Command Line Operations
  * Finding Linux Documentation
  * Processes
  * File Operations
* [Veri tabanı temelleri](https://www.youtube.com/watch?v=pHKZw5EyTi4&list=PLh9ECzBB8tJOS7WQKdeUaAa5fmPLYAouD)
* [ağ kavramları](https://www.youtube.com/playlist?list=PLDUOF2Be-kzn8h0-eI0HSADJsaDDu3IUj)
* [Konteynerler](https://www.youtube.com/watch?v=hUnST9FY39I)
* [Mikroservisler](https://www.youtube.com/watch?v=Jta9jw9kAVc)
* [Devops](https://www.youtube.com/watch?v=LI9FEQyopSg)
* [Devops](https://www.youtube.com/playlist?list=PLAocyzWJktm5nxI97bQaFiiXtWO8tawCC)
* [Gitops ve Kubernetes](https://www.youtube.com/watch?v=Utjni_LbMxc)
* [Netops] - 
* Docker ve konteyner imajları oluşturma 

## Temel Kavramlar
* [Neden Kontainerlar?](docs/neden.md)
* [Docker](docs/docker.md)
* [Bileşenler](docs/bilesen.md)
* [Küme Mimarisi](docs/mimari.md)
* [kubectl](docs/kubectl.md)

## Kurulum Alternatifleri 

- :trophy: Canlıya uygun, ölçeklenebilir, yönetilebilir. 
- :gift_heart: Beğendiklerim
- :rowboat: Sadece Geliştirme ve öğrenme ortamı için
- :bike: IOT ve Edge için

* [Kubespray](docs/kubespray.md) :trophy::gift_heart:
* [micro8s](docs/micro8s.md) :bike::gift_heart:
* [k0s](https://github.com/k0sproject/k0s) :bike: 
* [k3s](https://k3s.io/) :bike:
* [kind](https://kind.sigs.k8s.io/) :rowboat:
* [Docker Desktop](https://www.docker.com/products/docker-desktop) :rowboat:
* [Rancher Desktop](https://rancherdesktop.io/) :rowboat:
* [Minikube](https://minikube.sigs.k8s.io/docs/) :rowboat:


## Temel Kaynaklar

* [Pod,ReplicaSet,Deployment](docs/pod-rs-deployment.md)
* [Service](docs/service.md)
* [ConfigMaps](docs/configmaps.md)
* [Secrets](docs/secrets.md)

## Diğer Kaynaklar
* [Volumes](docs/volumes.md)
* [İşler ve Zamanlı İşler](docs/jobs-cronjobs.md)
* [DaemonSet](docs/daemonsets.md)
* [StatefulSet](docs/statefulsets.md)
* [Ingress](docs/ingress.md)
* [Yatay Pod Ölçeklendirme](docs/hpa.md)
* [Yetkiler](docs/yetkiler.md)
* [Kaynaklar ve Limitler](docs/kaynaklar-limitler.md)
* [CRD](docs/crd.md) - 

## Önemli Kavramlar
* [Label](docs/label.md)
* [İtme ve Çekme](docs/taints-affinity.md)
* [Sürekli Güncellemeler](docs/surekli-guncellemeler.md)
* [Sağlık Kontrolü](docs/canlilik-hazirlik.md)

# Ek Araçlar
* [Yük Dengeleme](docs/metallb.md)
* [Kustomize](docs/kustomize.md)
* [Helm](docs/helm.md)
* [ArgoCD](docs/argocd.md)
* [Service Mesh](docs/service-mesh.md) - 

## Görevler
* [Güvenlik](docs/güvenlik.md)
* [Servis Özelliğinin Farklı Kullanımı](docs/lb-dis-servisler.md)
* [Günlük Kayıtları](docs/logs.md)
* [Service Nasıl Çalışır?](docs/service-nasil.md)
* [Canlı ortam Kontrol Listesi](docs/prod_checklist.md)
* [İyi pratikler](docs/best-practices.md)


# Uygulama Kaynakları

* [Redhat Scholars](https://redhat-scholars.github.io/kubernetes-tutorial/kubernetes-tutorial/index.html)
* [Kodlar](https://github.com/redhat-scholars/kubernetes-tutorial.git) 
* [Just me and Opensource](https://github.com/justmeandopensource/kubernetes/tree/master/yamls)
* [Building a Kubernetes cluster in Microsoft Azure](https://github.com/ksachdeva/k8s-examples)
* [katacoda](https://www.katacoda.com/learn?q=kubernetes&hPP=12&idx=scenarios&p=0&is_v=1)
* [kubernetes the hard way](https://github.com/kelseyhightower/kubernetes-the-hard-way)


# Güzel Araçlar


* [Loft](https://loft.sh/) - Selfservice kubernetes hizmeti
* [Lens](https://k8slens.dev/) - Bir çok özelliği olan Kubernetes Masaüstü Yönetim Arayüzü
* [k9scli](https://k9scli.io/) - Havalı K8s terminali
* [KubeVirt](https://kubevirt.io/) - Konteynerleştiremediğin iş yükünü yöneteceksin.
* [Argo CD](https://argo-cd.readthedocs.io/en/stable/) -  Kubernetes için Declarative GitOps CD 
