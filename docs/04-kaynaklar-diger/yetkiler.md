---
layout: default
title: Yetkiler
nav_order: 5
parent: Diğer Kaynaklar
---

# users


```yaml
## kullanıcı
---
apiVersion: v1
kind: ServiceAccount
metadata:
  name: devs
  namespace: myspace


# role
---
kind: Role
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: devs-full-access
  namespace: myspace
rules:
- apiGroups: ["", "extensions", "apps"]
  resources: ["*"]
  verbs: ["*"]
- apiGroups: ["batch"]
  resources:
  - jobs
  - cronjobs
  verbs: ["*"]


# rolebinding
---
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: devs-user-view
  namespace: myspace
subjects:
- kind: ServiceAccount
  name: devs
  namespace: myspace
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: devs-full-access

```


```sh
kubectl create -f devs.yaml

```

User Secret'ini al.

```sh

# PLACE USER TOKEN HERE
# Kullanıcı Tokenı 
{% raw %}
kubectl -n mys get secret $(kubectl -n myspace get sa/devs -o jsonpath="{.secrets[0].name}") -o go-template="{{.data.token | base64decode}}"
{% endraw %}

# PLACE CERTIFICATE HERE
# kullanıcı sertifikası
kubectl -n myspace get secret $(kubectl -n myspace get sa/devs -o jsonpath="{.secrets[0].name}") -o "jsonpath={.data['ca\.crt']}"


# PLACE CERTIFICATE HERE
# superuser kubeconfigte tek cluster tanımlı ise 
kubectl config view --flatten=true  -o jsonpath='{.clusters[0].cluster.certificate-authority-data}'

```

kubeconfig dosyasının içine yukardaki çıktıları yerleştir.


```yaml
apiVersion: v1
kind: Config
preferences: {}

# Define the cluster
clusters:
- cluster:
    certificate-authority-data: PLACE CERTIFICATE HERE
    # You'll need the API endpoint of your Cluster here:
    server: https://YOUR_KUBERNETES_API_ENDPOINT
  name: my-cluster

# Define the user
users:
- name: mynamespace-user
  user:
    as-user-extra: {}
    client-key-data: PLACE CERTIFICATE HERE
    token: PLACE USER TOKEN HERE

# Define the context: linking a user to a cluster
contexts:
- context:
    cluster: my-cluster
    namespace: mynamespace
    user: mynamespace-user
  name: mynamespace

# Define current context
current-context: mynamespace
```
https://github.com/cruise-automation/rbacsync
https://medium.com/cruise/open-sourcing-rbacsync-48758df685b0