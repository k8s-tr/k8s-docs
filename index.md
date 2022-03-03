---
layout: default
title: Ana Sayfa
nav_order: 0
---


# Kubernetes Eğitimi

# [Ön Koşullar](docs/prerequests.html)

## Temel Kavramlar
* [Neden Kontainerlar?](docs/01-temel-kavramlar/neden.html)
* [Docker](docs/01-temel-kavramlar/docker.html)
* [Bileşenler](docs/01-temel-kavramlar/bilesen.html)
* [Küme Mimarisi](docs/01-temel-kavramlar/mimari.html)
* [kubectl](docs/01-temel-kavramlar/kubectl.html)

## Kurulum Alternatifleri 

* [Kubespray](docs/02-kurulum/kubespray.html) - Canlıya uygun, ölçeklenebilir, yönetilebilir. 
* [micro8s](docs/02-kurulum/micro8s.html) - IOT ve Edge için, Canlıya uygun, ölçeklenebilir, yönetilebilir. 
* [k0s](https://github.com/k0sproject/k0s) - IOT ve Edge için
* [k3s](https://k3s.io/) - IOT ve Edge için
* [kind](https://kind.sigs.k8s.io/) - Sadece Geliştirme ve öğrenme ortamı için
* [Docker Desktop](https://www.docker.com/products/docker-desktop) - Sadece Geliştirme ve öğrenme ortamı için
* [Rancher Desktop](https://rancherdesktop.io/) - Sadece Geliştirme ve öğrenme ortamı için
* [Minikube](https://minikube.sigs.k8s.io/docs/) - Sadece Geliştirme ve öğrenme ortamı için

## Temel Kaynaklar

* [Pod,ReplicaSet,Deployment](docs/03-kaynaklar-temel/pod-rs-deployment.html)
* [Service](docs/03-kaynaklar-temel/service.html)
* [ConfigMaps](docs/03-kaynaklar-temel/configmaps.html)
* [Secrets](docs/03-kaynaklar-temel/secrets.html)

## Diğer Kaynaklar

* [Volumes](docs/04-kaynaklar-diger/volumes.html)
* [İşler ve Zamanlı İşler](docs/04-kaynaklar-diger/jobs-cronjobs.html)
* [DaemonSet](docs/04-kaynaklar-diger/daemonsets.html)
* [StatefulSet](docs/04-kaynaklar-diger/statefulsets.html)
* [Ingress](docs/04-kaynaklar-diger/ingress.html)
* [Yatay Pod Ölçeklendirme](docs/04-kaynaklar-diger/hpa.html)
* [Yetkiler](docs/04-kaynaklar-diger/yetkiler.html)
* [Kaynaklar ve Limitler](docs/04-kaynaklar-diger/kaynaklar-limitler.html)
* [CRD](docs/04-kaynaklar-diger/crd.html)

## Önemli Kavramlar
* [Label](docs/05-onemli-kavramlar/label.html)
* [İtme ve Çekme](docs/05-onemli-kavramlar/taints-affinity.html)
* [Sürekli Güncellemeler](docs/05-onemli-kavramlar/surekli-guncellemeler.html)
* [Sağlık Kontrolü](docs/05-onemli-kavramlar/canlilik-hazirlik.html)

# Ek Araçlar
* [Yük Dengeleme](docs/06-ek-araclar/metallb.html)
* [Kustomize](docs/06-ek-araclar/kustomize.html)
* [Helm](docs/06-ek-araclar/helm.html)
* [ArgoCD](docs/06-ek-araclar/argocd.html)
* [Service Mesh](docs/06-ek-araclar/service-mesh.html) 

## Görevler
* [Güvenlik](docs/07-gorevler/güvenlik.html)
* [Servis Özelliğinin Farklı Kullanımı](docs/07-gorevler/lb-dis-servisler.html)
* [Günlük Kayıtları](docs/07-gorevler/logs.html)
* [Service Nasıl Çalışır?](docs/07-gorevler/service-nasil.html)
* [Canlı ortam Kontrol Listesi](docs/07-gorevler/prod_checklist.html)
* [İyi pratikler](docs/07-gorevler/best-practices.html)
* [Sertifika](docs/07-gorevler/cka.html)

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