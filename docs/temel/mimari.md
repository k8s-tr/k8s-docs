---
layout: default
title: Küme Mimarisi
parent: Temel Kavramlar
nav_order: 4
---

# Küme Mimarisi

https://azuredays.com/2020/12/09/understanding-kubernetes-workload-objects/

## Düğümler (Nodes)

Kubernetes, iş yükünüz için ürettiğiniz konteynerleri Pod'lara yerleştirerek Nodlarda çalıştırır. Bir düğüm, sanal veya fiziksel bir makine olabilir. Her düğüm, kontrol düzlemi tarafından yönetilen Pod'ları çalıştırmak için gerekli servislere sahiptir. Genellikle bir kümede birkaç düğüm vardır.

Bir düğümdeki bileşenler: 
* kubelet, 
* konteyner-runtime
* kube-proxy