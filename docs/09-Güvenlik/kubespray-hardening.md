---
layout: default
title: Kubespray Hardening
nav_order: 9
parent: Güvenlik
---

## Kubernetes Hardening

* CIS kurallarına göre hardening parametreleri 

https://github.com/kubernetes-sigs/kubespray/blob/master/docs/operations/hardening.md

Bir deployment veya pod oluşturulmaya çalışılırsa aşağıdaki hatayı verecektir

```log

Error from server (Forbidden): pods "test" is forbidden: violates PodSecurity "restricted:v1.27": allowPrivilegeEscalation != false (container "test" must set securityContext.allowPrivilegeEscalation=false), unrestricted capabilities (container "test" must set securityContext.capabilities.drop=["ALL"]), runAsNonRoot != true (pod or container "test" must set securityContext.runAsNonRoot=true), seccompProfile (pod or container "test" must set securityContext.seccompProfile.type to "RuntimeDefault" or "Localhost")
 how to make exception

```

* Default hardening kurallarına uygun deployment tanımı
* bu kurallar için uygun imajında root kullanıcısı ile çalışmıyor olması gerekir. Yoksa öyle bir hata verecektir. 

```bash 

kubectl describe pod secure-deployment

  Warning  Failed     12s (x8 over 98s)   kubelet            Error: container has runAsNonRoot and image will run as root (pod: "secure-deployment-85c9947bdc-kgtk6_test(9e6a9b9f-aa47-4625-9bd6-ad519e03f127)", container: secure-container)

```

```yaml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: secure-deployment
  labels:
    app: secure-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: secure-app
  template:
    metadata:
      labels:
        app: secure-app
    spec:
      containers:
      - name: secure-container
        image: nginxinc/nginx-unprivileged:latest
        securityContext:
          allowPrivilegeEscalation: false
          capabilities:
            drop: ["ALL"]
          runAsNonRoot: true
          seccompProfile:
            type: RuntimeDefault


```

* istisnalar sadece ns düzeyinde yaratılabilir. tekil pod/deployment için çalışmaz

```yaml

apiVersion: v1
kind: Namespace
metadata:
  labels:
    kubernetes.io/metadata.name: loy
    pod-security.kubernetes.io/audit: baseline
    pod-security.kubernetes.io/enforce: baseline
    pod-security.kubernetes.io/exempt: "true"
    pod-security.kubernetes.io/warn: baseline
  name: istisna-ns


```

### Hardening aktif ise 

* Kubespray hardening config'i bileşen bazında şu değişiklikleri getiriyor:

---

#### kube-apiserver


- `authorization_modes: ['Node', 'RBAC']` — zaten default, ama açıkça sabitleniyor
- `kube_apiserver_request_timeout: 120s`
- `kube_apiserver_service_account_lookup: true` — token'ın hâlâ geçerli bir SA'ya ait olup olmadığını kontrol eder
- **Audit logging açılır** — `/var/log/kube-apiserver-log.json`, 30 gün / 10 backup / 100MB
- **TLS minimum TLS 1.2**, kısıtlı cipher suite listesi
- **Secrets encryption at rest** — `secretbox` algoritmasıyla, veriler etcd'ye yazılmadan önce şifrelenir
- **Admission plugin'leri:** `EventRateLimit`, `AlwaysPullImages`, `ServiceAccount`, `NamespaceLifecycle`, `NodeRestriction`, `LimitRanger`, `ResourceQuota`, `MutatingAdmissionWebhook`, `ValidatingAdmissionWebhook`, `PodSecurity`


**Operasyonel etkisi:**
- `AlwaysPullImages` — private registry kullanan ortamlarda her pod başlangıcında image pull yapılır, bu gecikmeye ve registry bağımlılığına neden olur
- `EventRateLimit` — ek configmap gerektirir, eksik olursa apiserver başlamaz
- Secrets encryption at rest açıldıktan sonra mevcut secret'ları şifrelemek için `kubectl get secrets --all-namespaces -o json | kubectl replace -f -` çalıştırmak gerekir

---

#### kubelet


- `kubelet_authorization_mode_webhook: true` ve `kubelet_authentication_token_webhook: true`
- `kube_read_only_port: 0` — read-only port kapatılır (default 10255)
- `kubelet_rotate_server_certificates: true` ve `kubelet_rotate_certificates: true`
- `kubelet_protect_kernel_defaults: true`
- `kubelet_event_record_qps: 1`
- `kubelet_streaming_connection_idle_timeout: "5m"`
- `kubelet_seccomp_default: true`
- `kubelet_systemd_hardening: true` — node üzerinde minimal iptables firewall kurar, `kubelet_secure_addresses` ile birlikte çalışır


**Operasyonel etkisi:**
- `kubelet_rotate_server_certificates: true` açılırsa kurulum sonrası `kubectl get csr` ile pending CSR'ları approve etmek gerekir, aksi halde node'lar NotReady kalır
- `kubelet_systemd_hardening` ile birlikte `kubelet_secure_addresses` doğru set edilmezse node'lar birbirini veya apiserver'ı göremeyebilir
- `kubelet_protect_kernel_defaults: true` — node'un kernel parametreleri kubelet'in beklentisiyle uyuşmuyorsa kubelet başlamayı reddeder

---

#### etcd


- `etcd_deployment_type: host` — etcd container yerine doğrudan host process olarak çalışır


Bu özellikle mevcut bir cluster'da sonradan değiştirilemez, kurulum zamanı kararıdır.

---

#### scheduler ve controller-manager


Her ikisinin de `bind_address` `127.0.0.1`'e çekilir, yani dışarıdan erişilemez hale gelir.


Prometheus gibi dışarıdan scrape eden bir monitoring stack varsa bunu dikkate almak gerekir.

---

#### Özet: En çok dikkat gerektiren noktalar

| Alan | Risk |
|---|---|
| `AlwaysPullImages` | Registry erişimi kesilirse pod'lar ayağa kalkmaz |
| `kubelet_rotate_server_certificates` | Kurulum sonrası CSR approve adımı unutulursa node'lar NotReady |
| `kubelet_systemd_hardening` + `kubelet_secure_addresses` | Yanlış IP konfigürasyonu cluster iletişimini keser |
| Secrets encryption at rest | Mevcut secret'lar retroactive şifrelenmez, manuel adım gerekir |
| `etcd_deployment_type: host` | Yeni kurulumda karar verilmeli, sonradan değiştirmek cluster rebuild gerektirir |

### Kaynaklar
* https://kubernetes.io/docs/concepts/security/pod-security-admission/
* https://kubernetes.io/docs/tasks/configure-pod-container/enforce-standards-namespace-labels/