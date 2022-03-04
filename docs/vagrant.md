---
layout: default
title: Vagrant Kurulumu
parent: 
nav_order: 0
parent: Kurulum
---

## Virtualbox ve Vagrant kurulumu
İndirin ve kurun

## Vagrant Kurulumu

Vagrant kullanımı
Vagrant masaüstü ortamları için kod ile otomatik sanal makine oluşturma uygulamasıdır. Varsayılan olarak virtualbox kullanır. Windows ve linux hostlar üzerinde çalışır.
* Bir yerde sanal makinemizi tanıtıcı bir dizin oluşturup (Örn. vm01) komut satırından dizine gidiyoruz ve  aşağıdaki komutu çalıştırıyoruz. Komut, eğer makinede yoksa centos7 imajını vagrant reposundan indirir.

```
vagrant init generic/ubuntu2004
```
Yukarıdaki komut bulunulan dizinde Vagrantfile adında bir config dosyası oluşturur. Dosyanın içini silip aşağıdaki satırları ekleyin ve hostname ve ip karşısındaki alanları değiştirin. Uygun ip blokları için [buraya](https://www.wikiwand.com/en/Private_network#/Private_IPv4_address_spaces) bakınız.


![Vagrant virtualbox ağ yapısı](https://user-images.githubusercontent.com/4180560/79636826-3e0d9d80-8183-11ea-8ced-eed33d53e184.png)

[Örnek Vagrantfile](kaynaklar/Vagrantfile)

Dosyadaki ```NUMBER_OF_MEMBERS``` sayısını artırarak istediğimiz sayıda sanal makine üretebiliriz.

Bu dosyadan sanal makine aktif etmek için aşağıdaki komutla poweron ederiz.

```
vagrant up
```
poweron süreci bittikten sonra ssh ile kendi yönlendirmesiyle bağlanabiliriz.Masaüstü komut satırını açıyoruz ve bu ip adresine ssh erişimi yapıyoruz.
```
ssh vagrant@<sanal_makine_ip>
# varsayılan parola: "vagrant"

```