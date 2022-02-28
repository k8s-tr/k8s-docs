---
layout: default
title:  Kaynaklar ve Limitler
nav_order: 3
parent: Diğer Kaynaklar
---

# Kaynaklar ve Limitler

```sh
kubectl config set-context --current --namespace=myspace

kubectl get all

No resources found in myspace namespace.

```

```yaml

https://github.com/edib/kubernetes/blob/master/yamls/7-pod-quota-mem-exceed.yaml

https://github.com/edib/kubernetes/blob/master/yamls/7-pod-quota-mem.yaml

https://github.com/edib/kubernetes/blob/master/yamls/7-quota-count.yaml

https://github.com/edib/kubernetes/blob/master/yamls/7-quota-limitrange.yaml


https://github.com/edib/kubernetes/blob/master/yamls/7-quota-mem.yaml


```

Uygulamamızı kuruyoruz. 

```sh
kubectl apply -f https://raw.githubusercontent.com/redhat-scholars/kubernetes-tutorial/master/apps/kubefiles/myboot-deployment.yml
```

Podun detaylarına bakalım.

```sh
PODNAME=$(kubectl get pod -l app=myboot -o name)
kubectl describe $PODNAME

Name:         myboot-7cbfbd9b89-7q97b
Namespace:    funstuff
Priority:     0
Node:         node1/10.0.0.246
Start Time:   Mon, 04 Oct 2021 09:28:36 +0000
Labels:       app=myboot
              pod-template-hash=7cbfbd9b89
Annotations:  cni.projectcalico.org/podIP: 10.1.166.154/32
              cni.projectcalico.org/podIPs: 10.1.166.154/32
Status:       Running
IP:           10.1.166.154
IPs:
  IP:           10.1.166.154
Controlled By:  ReplicaSet/myboot-7cbfbd9b89
Containers:
  myboot:
    Container ID:   containerd://c8fdbebe0b0a4169f5c7cd630beb5028604e473e38ea1473ca117c697a3e13e8
    Image:          quay.io/rhdevelopers/myboot:v1
    Image ID:       quay.io/rhdevelopers/myboot@sha256:ea9a142b694725fc7624cda0d7cf5484d7b28239dd3f1c768be16fc3eb7f1bd0
    Port:           8080/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Mon, 04 Oct 2021 09:29:36 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-rhdmf (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             True 
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  kube-api-access-rhdmf:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age    From               Message
  ----    ------     ----   ----               -------
  Normal  Scheduled  4m34s  default-scheduler  Successfully assigned funstuff/myboot-7cbfbd9b89-7q97b to node1
  Normal  Pulling    4m33s  kubelet            Pulling image "quay.io/rhdevelopers/myboot:v1"
  Normal  Pulled     3m35s  kubelet            Successfully pulled image "quay.io/rhdevelopers/myboot:v1" in 58.347827289s
  Normal  Created    3m34s  kubelet            Created container myboot
  Normal  Started    3m34s  kubelet            Started container myboot

```
Ayağa kalktı.
Siliyoruz.

```sh
kubectl delete deployment myboot
```

Kaynak tanımları olanı kuruyoruz.

```sh
kubectl apply -f https://raw.githubusercontent.com/redhat-scholars/kubernetes-tutorial/master/apps/kubefiles/myboot-deployment-resources.yml
```
Podun durumuna bakalım.
```sh
kubectl get pods

NAME                      READY   STATUS    RESTARTS   AGE
myboot-7b7d754c86-kjwlr   0/1     Pending   0          19s
```
Daha fazla bilgi almak istiyorsak

```sh
kubectl get events --sort-by=.metadata.creationTimestamp

<unknown>   Warning   FailedScheduling    pod/myboot-7b7d754c86-kjwlr    0/6 nodes are available: 6 Insufficient cpu.
<unknown>   Warning   FailedScheduling    pod/myboot-7b7d754c86-kjwlr    0/6 nodes are available: 6 Insufficient cpu.
```

Kaynaklarda belirtilen kadar İşlemci ve bellek yoksa uygulama ayağa kalkmaz.

pod açıklamalarından da aynı bilgilere erişebilirsiniz.

```sh
PODNAME=$(kubectl get pod -l app=myboot -o name)
kubectl describe $PODNAME
```
Uygulamamızı siliyoruz.

```sh
kubectl delete -f https://raw.githubusercontent.com/redhat-scholars/kubernetes-tutorial/master/apps/kubefiles/myboot-deployment-resources.yml
```

Limitleri olan başka bir uygulama kuruyoruz.

```sh
kubectl apply -f https://raw.githubusercontent.com/redhat-scholars/kubernetes-tutorial/master/apps/kubefiles/myboot-deployment-resources-limits.yml

```

İnceleyelim ve ayakta olduğunu görelim.
```sh
PODNAME=$(kubectl get pod -l app=myboot -o name)
kubectl describe $PODNAME
```

Servisini kuralım.
```sh
kubectl apply -f https://raw.githubusercontent.com/redhat-scholars/kubernetes-tutorial/master/apps/kubefiles/myboot-service.yml

```
curl ile test edelim.
```sh
# ip ve portu öğreniyoruz.
kubectl get svc myboot
NAME     TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
myboot   LoadBalancer   10.152.183.11   <pending>     8080:32299/TCP   6h38m

while true
do curl $IP:$PORT
sleep .3
done
```

Referanslar
https://kubernetes.io/docs/tasks/configure-pod-container/assign-cpu-resource/
https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/
