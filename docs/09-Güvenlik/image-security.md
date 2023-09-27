---
layout: default
title: Imaj Güvenliği
nav_order: 4
parent: Güvenlik
---

# Imaj Güvenliği

## Deterministik docker  imaj etiketlerini kullanın

* Genel imaj takma adları yerine SHA256 hash olanları veya deterministik imaj  sürümlerini kullanın.
* Örnek: maven:3.6.3-jdk-11-slim@sha256:68ce1cd457891f
* İşletim sistemlerinin bir sürümlerini aşağıdaki gibi alarak deterministik bir sürüm alabiliriz.
Yalnızca ihtiyacınız olanı kurun

Bir JDK'ya, Java koduna veya Maven ve Gradle gibi bir derleme aracına ihtiyacınız yoktur. Bunun yerine,

        Sadece WAR ya da Jar dosyasınızı kopyalayın.
        JRE ürününü veya imajnını kullanın.


Uygulamaların koşacağı imajı bu şekilde üretirsek sadece jre kurmuş oluyoruz. Ayrıca boyut 120MB azalıyor.


FROM alpine:3.15

RUN apk --no-cache add openjdk17-jre-headless bash 


Java Docker imajlarınızdaki güvenlik açıklarını bulun ve düzeltin.

Docker imajları, güvenlik açıklarını içerebilir.

Güvenlik açıklarını ücretsiz olan Snyk aracıyla  tarayın ve düzeltin

npm install -g snyk  
snyk auth
snyk container test myimage --file=Dockerfile

https://snyk.io/product/open-source-security-management/


Multistage buildler kullanın

Eğer build işlemlerini konteynır içinde yapıyorsanız 2 imajın ayrı olması gerekir.
Properly handle events to safely terminate a Java application

pid-1 de çalışacak şekilde kullanmayın.

CMD “java” “-jar” “application.jar”
CMD “start-app.sh”


Lite bir init desteği kullanın. Bu sayede Java prosesine sağlıklı bir şekilde sinyal gönderebilirsiniz. Java processi signalleri handle edemediği için öldürme isteklerine cevap vermeyebiliyor bazı durumlarda. Container ölemiyor. Ama dumb-init gibi bir araç kernelden gelen sinyalleri işleyebildiği için java processini öldürüyor ve sonra da kendisi de ölüyor.


CMD “dumb-init” “java” “-jar” “application.jar”

https://github.com/Yelp/dumb-init
Java appleri root yetkisi ile değil standart kullanıcı ile çalıştırın.
Java uygulamalarını uygunca öldürün


Çalışan bir Java uygulamasını ani bir şekilde sonlandırmak önlemek, aktif canlı bağlantıları da durdurur.  Kapatmak için uygulama içinde kapatma mesajları gönderin. 

Runtime.getRuntime().addShutdownHook
(yourShutdownThread);

.dockerignore kullanın

build sırasındaki ve öncesinde oluşturulan veya oluşan dosyalar yanlışlıkla imajla beraber gitmez.
Konteynırda olduğunu anlayan Java kullanın

Eski JVM sürümleri Docker memory and CPU
ayarlarına uymaz.
Java 10+ veya Java 8 update 191+ üzeri kullanın.
Automatic Docker konteynır üretme araçlarını kullanırken dikkatli olun

Spring Boot 2.3 Docker image oluşturma ve JIB gibi araçlar otomatik olarak imaj oluşturabilir ama ek güvenlik özelliklerini uygulayamazlar. Bu yüzden, bunların yerine gerekli güvenlik yapılandırmasını içeren Dockerfile kullanın.


Kaynak

https://snyk.io/wp-content/uploads/10-best-practices-to-containerize-Java-applications-with-Docker.pdf

https://snyk.io/blog/docker-for-java-developers/

https://www.tutorialworks.com/docker-java-best-practices/

https://akobor.me/posts/heap-size-and-resource-limits-in-kubernetes-for-jvm-applications (kaynaklar ve xms açısından yaklaşmış)

https://medium.com/marionete/managing-java-heap-size-in-kubernetes-3807159e2438

https://labs.bishopfox.com/tech-blog/bad-pods-kubernetes-pod-privilege-escalation (pod güvenliği ile ilgili geniş bir çalışma) ***

https://infosecwriteups.com/kubernetes-container-escape-with-hostpath-mounts-d1b86bd2fa3