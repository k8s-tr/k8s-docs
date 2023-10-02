---
layout: default
title: Admission Policy
nav_order: 2
parent: Güvenlik
---

```bash

kubectl create -f https://github.com/kyverno/kyverno/releases/download/v1.10.3/install.yaml

```
# Validation

Doğrulama politikaları, Kubernetes kaynaklarını yaratma veya güncelleme sırasında uygulanır. Bu politikalar, kaynakların belirli kurallara ve standartlara uygun olup olmadığını kontrol eder. Eğer bir kaynak, belirli bir doğrulama politikasına uymazsa, bu kaynağın oluşturulması veya güncellenmesi reddedilir.

Örnek: Pod'ların sadece belirli imaj depolarından imaj çekmesini zorunlu kılmak için bir doğrulama politikası oluşturabilirsiniz.

```yaml
apiVersion: kyverno.io/v1
kind: Policy
metadata:
  name: restrict-image-sources
spec:
  validationFailureAction: Enforce
  rules:
  - name: check-image-source
    match:
      resources:
        kinds:
        - Pod
    validate:
      message: "Images must be pulled from your-allowed-registry.com"
      pattern:
        spec:
          containers:
          - image: "your-allowed-registry.com/*"
```

### Politika Açıklaması:

1. `kind: ClusterPolicy`: Bu politika bir `ClusterPolicy` türündedir, yani tüm cluster'da uygulanır.
2. `name: restrict-image-sources`: Politikanın adı.
3. `validationFailureAction: enforce`: Bu politika zorlayıcı (enforcing) modda çalışır, yani kurala uymayan Pod'lar reddedilir.
4. `match.resources.kinds: - Pod`: Bu politika sadece `Pod` türündeki kaynaklara uygulanır.
5. `validate.message`: Kurala uymayan kaynaklar için gösterilecek hata mesajı.
6. `pattern.spec.containers.image`: Pod tanımında bulunan her bir container’ın `image` alanı için beklenen patern. Bu örnekte, `your-allowed-registry.com/` ile başlayan imaj adlarına izin verilir.


* Örnek-2: her oluşturulan podta `team` anahtarını içeren bir etiket olmasını bekler. 

```bash
kubectl create -f- << EOF
apiVersion: kyverno.io/v1
kind: Policy
metadata:
  name: require-labels
spec:
  validationFailureAction: Enforce
  rules:
  - name: check-team
    match:
      any:
      - resources:
          kinds:
          - Pod
    validate:
      message: "label 'team' is required"
      pattern:
        metadata:
          labels:
            team: "?*"
EOF

```

* Test
```bash
kubectl create deployment nginx --image=nginx


kubectl run nginx --image nginx --labels team=backend

kubectl get policyreport
```

* deny unkown repositories

```yaml

apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: allowed-repo
spec:
  validationFailureAction: Enforce
  rules:
  - name: check-registries
    match:
      resources:
        kinds:
        - Pod
    validate:
      message: "Registry not allowed"
      pattern:
        spec:
          containers:
          - image: "docker.io/* | quay.io/*"


```

* deny privileged pods

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: disallow-privileged-containers
spec:
  validationFailureAction: Enforce
  background: false
  rules:
    - name: prevent-privileged-containers
      match:
        resources:
          kinds:
            - Pod
      validate:
        message: "Privileged containers are not allowed"
        pattern:
          spec:
            containers:
              - =(securityContext):
                  =(privileged): false
```


# Mutation

Mutasyon politikaları, kaynakların yaratılma veya güncelleme sırasında dinamik olarak değiştirilmesini sağlar. Bu politikalar, kaynak tanımlarına otomatik olarak alanlar ekler, mevcut alanları değiştirir veya alanları kaldırır.

Kyverno’da `mutate` bölümü, Kubernetes kaynaklarını dinamik olarak değiştirmek için kullanılır. Bu, kaynak tanımlarına otomatik olarak alanlar eklemek, mevcut alanları değiştirmek veya alanları kaldırmak için kullanılır. Mutasyon için iki temel yöntem vardır: `patchStrategicMerge` ve `overlay`.

### 1. **patchStrategicMerge:**
`patchStrategicMerge` yöntemi, bir kaynak tanımına spesifik alanları eklemek veya değiştirmek için kullanılır. Bu yöntemle, belirli alanlara yapılan değişiklikler tanımlandığı gibi uygulanır.

#### Örnek:

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: add-security-context
spec:
  rules:
  - name: patch-security-context
    match:
      resources:
        kinds:
        - Pod
    mutate:
      patchStrategicMerge:
        spec:
          securityContext:
            runAsNonRoot: true
            runAsUser: 1000
```

Bu örnekte, Pod’lara `securityContext` eklenir ve `runAsNonRoot: true` ve `runAsUser: 1000` olarak ayarlanır.

### 2. **overlay:**
`overlay` yöntemi, bir kaynak üzerine daha geniş ve kapsamlı değişiklikler yapmak için kullanılır. `overlay` daha kompleks ve detaylı mutasyonlar için uygundur ve `patchStrategicMerge` ile benzer şekilde çalışır ancak daha fazla seçenek sunar.

#### Örnek:

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: add-labels-and-annotations
spec:
  rules:
  - name: patch-labels-annotations
    match:
      resources:
        kinds:
        - Pod
    mutate:
      overlay:
        metadata:
          labels:
            my-label: my-label-value
          annotations:
            my-annotation: my-annotation-value
```

Bu örnekte, Pod’lara `my-label: my-label-value` etiketi ve `my-annotation: my-annotation-value` notu eklenir.

### Farklar:
- `patchStrategicMerge` daha basit ve spesifik alan değişiklikleri için uygundur.
- `overlay` daha kapsamlı ve detaylı mutasyonlar yapmak için kullanılır ve daha fazla esneklik sunar.

Her iki yöntem de benzer amaçlar için kullanılabilir, ve hangi yöntemin kullanılacağı spesifik kullanım durumunuza ve ihtiyacınıza bağlıdır. Genellikle, daha basit ve spesifik mutasyonlar için `patchStrategicMerge`, daha kapsamlı ve detaylı mutasyonlar için `overlay` kullanılır.



Pod tanımlarına otomatik olarak bir güvenlik politikası eklemek için kullanabileceğiniz bir Kyverno mutasyon politikası örneği bulunmaktadır. Bu örnekte, her Pod'a otomatik olarak bir `securityContext` eklenmektedir.

```yaml
apiVersion: kyverno.io/v1
kind: Policy
metadata:
  name: add-security-context
spec:
  rules:
    - name: add-securityContext
      match:
        resources:
          kinds:
          - Pod
      mutate:
        overlay:
          spec:
            securityContext:
              runAsNonRoot: true
              runAsUser: 1000
```

### Politika Açıklaması:

1. `kind: ClusterPolicy`: Bu politika bir `ClusterPolicy` türündedir, yani tüm cluster'da uygulanır.
2. `name: add-security-context`: Politikanın adı.
3. `match.resources.kinds: - Pod`: Bu politika sadece `Pod` türündeki kaynaklara uygulanır.
4. `mutate.overlay.spec.securityContext`: Mutasyon işlemi sırasında Pod’a eklenecek `securityContext` tanımlanmıştır. Bu örnekte, her Pod’un `runAsNonRoot: true` ve `runAsUser: 1000` olarak ayarlanacak bir `securityContext` alması sağlanmaktadır.


Burada `team: bravo` şeklinde anahtar-değerli bir etiket eklenmektedir. 

```bash
kubectl create -f- << EOF
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: add-labels
spec:
  rules:
  - name: add-team
    match:
      any:
      - resources:
          kinds:
          - Pod
    mutate:
      patchStrategicMerge:
        metadata:
          labels:
            +(team): bravo
EOF

kubectl run redis --image redis

kubectl get pod redis --show-labels

kubectl run newredis --image redis -l team=alpha

kubectl get pod myredis --show-labels

```

## Generation

Üretim politikaları, diğer Kubernetes kaynaklarının yaratılmasına veya silinmesine yanıt olarak otomatik olarak kaynaklar üretir. Bu, belirli bir kaynağın yaratılmasına veya silinmesine yanıt olarak başka kaynakların da dinamik olarak yönetilmesini sağlar.

Örnek: Her yeni Namespace için otomatik olarak bir Role veya RoleBinding oluşturmak üzere bir üretim politikası kullanabilirsiniz.

Örnek Politika:

Aşağıda bir Validation politika örneği bulunmaktadır:

```bash
kubectl -n default create secret docker-registry regcred \
  --docker-server=myinternalreg.corp.com \
  --docker-username=john.doe \
  --docker-password=Passw0rd123! \
  --docker-email=john.doe@corp.com

kubectl create -f- << EOF
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: sync-secrets
spec:
  rules:
  - name: sync-image-pull-secret
    match:
      any:
      - resources:
          kinds:
          - Namespace
    generate:
      apiVersion: v1
      kind: Secret
      name: regcred
      namespace: "{{request.object.metadata.name}}"
      synchronize: true
      clone:
        namespace: default
        name: regcred
EOF


kubectl create ns mytestns


kubectl -n mytestns get secret

kubectl delete clusterpolicy sync-secrets

```

* Her yeni Namespace için otomatik olarak bir Role veya RoleBinding oluşturmak üzere bir üretim politikası kullanabilirsiniz.

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: generate-role-and-rolebinding
spec:
  rules:
    - name: create-default-role
      match:
        resources:
          kinds:
          - Namespace
      generate:
        kind: Role
        name: default-role
        namespace: "{{request.object.metadata.name}}"
        data:
          rules:
            - apiGroups: [""]
              resources: ["pods"]
              verbs: ["get", "list"]
    - name: create-rolebinding-for-default-role
      match:
        resources:
          kinds:
          - Namespace
      generate:
        kind: RoleBinding
        name: default-role-binding
        namespace: "{{request.object.metadata.name}}"
        data:
          subjects:
            - kind: Group
              name: 'system:authenticated'
              apiGroup: 'rbac.authorization.k8s.io'
          roleRef:
            kind: Role
            name: default-role
            apiGroup: 'rbac.authorization.k8s.io'
```

### Politika Açıklaması:

- İki kural içeren bir `ClusterPolicy` oluşturulmuştur: `create-default-role` ve `create-rolebinding-for-default-role`.
- Her iki kural da `Namespace` kaynak türüyle eşleşir, yani yeni bir `Namespace` oluşturulduğunda tetiklenirler.
- `create-default-role` kuralı:
  - `generate` bölümü kullanarak bir `Role` oluşturur.
  - Bu `Role`, oluşturulan `Namespace` içinde `default-role` adını alır.
  - Oluşturulan `Role`'de `pods` kaynağı için `get` ve `list` yetkileri verilir.
- `create-rolebinding-for-default-role` kuralı:
  - Benzer şekilde, `generate` bölümü kullanarak bir `RoleBinding` oluşturur.
  - `RoleBinding`, oluşturulan `Namespace` içinde `default-role-binding` adını alır ve `default-role`'e bağlanır.
  - `RoleBinding`, `system:authenticated` grubunu `default-role`'e bağlar.

### Uygulama Adımları:

1. Yukarıdaki YAML kodunu bir dosyaya yapıştırın, örneğin `generate-role-and-rolebinding.yaml` olarak adlandırabilirsiniz.
2. `kubectl apply -f generate-role-and-rolebinding.yaml` komutunu kullanarak politikayı uygulayın.
3. Bundan sonra, her yeni oluşturulan `Namespace` için otomatik olarak bir `Role` ve `RoleBinding` oluşturulacaktır.

### Not:
- Bu örnekteki `Role` ve `RoleBinding` tanımları örnek amaçlıdır; gerçek kullanım senaryonuza göre bu değerleri değiştirmelisiniz.
- `generate` kuralı, mevcut kaynaklar üzerinde herhangi bir etkiye sahip değildir; sadece yeni oluşturulan kaynaklara uygulanır.

# policy vs clusterpolicy
Biri namespace seviyesinde çalışırken diğeri  tüm küme için çalışır. 
