---
layout: default
title: Kubectl Shortcuts
nav_order: 3
parent: İleri Düzey Konular
---


# Kubectl Shortcuts


```
alias a='alias'
alias k='kubectl '
alias ka='kubectl apply -f '
alias kaf='kubectl apply -f '
alias kcf='kubectl create -f '
alias kd='kubectl delete '
alias kdf='kubectl delete -f '
alias kg='kubectl get '
alias kgd='kubectl get deployments'
alias kgn='kubectl get nodes' 
alias kgnl='kubectl get nodes --show-labels'
alias kgnw='kubectl get nodes -o wide'
alias kgp='kubectl get pods'
alias kgpa='kubectl get pods --all-namespaces'
alias kgs='kubectl get svc'
alias kl='kubectl logs'
alias klf='kubectl logs -f'
alias kns='function __kubens() { kubectl config set-context --current --namespace="$1"; }; __kubens'
alias kx='function __kexec() { kubectl exec -it $1 -- bash; }; __kexec'
alias kxa='function __kexec() { kubectl exec -it $1 -- ash; }; __kexec'
alias kxs='function __kexec() { kubectl exec -it $1 -- sh; }; __kexec'

```
