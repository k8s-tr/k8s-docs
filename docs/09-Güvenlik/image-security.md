---
layout: default
title: Imaj Güvenliği
nav_order: 4
parent: Güvenlik
---

# Imaj Güvenliği

## Deterministik docker  imaj etiketlerini kullanın

* Genel imaj takma adları yerine SHA256 hash olanları veya deterministik imaj  sürümlerini kullanın.
* FROM maven:3.6.3-jdk-11-slim@sha256:68ce1cd457891f48d1e137c7d6a4493f60843e84c9e2634e3df1d3d5b381d36c
* İşletim sistemlerinin bir sürümlerini aşağıdaki gibi alarak deterministik bir sürüm alabiliriz.

## Yalnızca ihtiyacınız olanı kurun

Bir JDK'ya, Java koduna veya Maven ve Gradle gibi bir derleme aracına ihtiyacınız yoktur. Bunun yerine,

        Sadece WAR ya da Jar dosyasınızı kopyalayın.
        JRE ürününü veya imajnını kullanın.

Uygulamaların koşacağı imajı bu şekilde üretirsek sadece jre kurmuş oluyoruz. Ayrıca boyut 120MB azalıyor.



## Java Docker imajlarınızdaki güvenlik açıklarını bulun ve düzeltin.

* https://snyk.io/product/open-source-security-management/
* trivy

## Multistage buildler kullanın

Eğer build işlemlerini konteynır içinde yapıyorsanız 2 imajın ayrı olması gerekir.

## Properly handle events to safely terminate a Java application (stability)

pid-1 de çalışacak şekilde kullanmayın.

CMD “java” “-jar” “application.jar”
CMD “start-app.sh”


Lite bir init desteği kullanın. Bu sayede Java prosesine sağlıklı bir şekilde sinyal gönderebilirsiniz. Java processi signalleri handle edemediği için öldürme isteklerine cevap vermeyebiliyor bazı durumlarda. Container ölemiyor. Ama dumb-init gibi bir araç kernelden gelen sinyalleri işleyebildiği için java processini öldürüyor ve sonra da kendisi de ölüyor.


CMD “dumb-init” “java” “-jar” “application.jar”

https://github.com/Yelp/dumb-init

Çalışan bir Java uygulamasını ani bir şekilde sonlandırmak önlemek, aktif canlı bağlantıları da durdurur.  Kapatmak için uygulama içinde kapatma mesajları gönderin. 

Runtime.getRuntime().addShutdownHook
(yourShutdownThread);

## Java appleri root yetkisi ile değil standart kullanıcı ile çalıştırın.


## .dockerignore kullanın

build sırasındaki ve öncesinde oluşturulan veya oluşan dosyalar yanlışlıkla imajla beraber gitmez.
Konteynırda olduğunu anlayan Java kullanın

##  Eski JVM sürümleri Docker memory and CPU ayarlarına uymaz.
Java 10+ veya Java 8 update 191+ üzeri kullanın.



## jib kullanımı

```
*********
   <plugin>     
            <groupId>com.google.cloud.tools</groupId>
            <artifactId>jib-maven-plugin</artifactId>
            <version>3.4.0</version>
            <configuration>
              <to>
                <image>spring-with-root</image>
              </to>
            </configuration>
          </plugin>
******** 

*********
   <plugin>     
            <groupId>com.google.cloud.tools</groupId>
            <artifactId>jib-maven-plugin</artifactId>
            <version>3.4.0</version>
            <configuration>
              <to>
                <image>spring-with-no-root</image>
              </to>
              <container>
                <user>5005:5005</user>
            </container>
            </configuration>
          </plugin>
******** 


mvn compile jib:build

mvn compile jib:dockerBuild

```

## node

```
# Use the official Node.js image as the base image
FROM node:14-slim

# Set the working directory
WORKDIR /usr/src/app

# Create a new user 'nodeuser' and use /usr/src/app as the home directory
RUN useradd -m -d /usr/src/app nodeuser

# Set ownership of /usr/src/app to 'nodeuser'
RUN chown -R nodeuser:nodeuser /usr/src/app

# Switch to 'nodeuser'
USER nodeuser

# Copy package.json and package-lock.json (if available)
COPY --chown=nodeuser:nodeuser package*.json ./

# Install dependencies
RUN npm install

# Copy the rest of the application code
COPY --chown=nodeuser:nodeuser . .

# Expose port (if necessary)
EXPOSE 3000

# Command to run the application
CMD ["npm", "start"]


```

#### Kaynaklar


https://snyk.io/blog/docker-for-java-developers/

https://snyk.io/blog/best-practices-to-build-java-containers-with-docker/

https://www.tutorialworks.com/docker-java-best-practices/

https://akobor.me/posts/heap-size-and-resource-limits-in-kubernetes-for-jvm-applications (kaynaklar ve xms açısından yaklaşmış)

https://medium.com/marionete/managing-java-heap-size-in-kubernetes-3807159e2438

https://labs.bishopfox.com/tech-blog/bad-pods-kubernetes-pod-privilege-escalation (pod güvenliği ile ilgili geniş bir çalışma) ***

https://infosecwriteups.com/kubernetes-container-escape-with-hostpath-mounts-d1b86bd2fa3