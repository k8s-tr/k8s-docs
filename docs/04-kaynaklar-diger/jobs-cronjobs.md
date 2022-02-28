---
layout: default
title:  Jobs & Cronjobs
nav_order: 2
parent: Diğer Kaynaklar
---

# Jobs & Cronjobs

https://redhat-scholars.github.io/kubernetes-tutorial/kubernetes-tutorial/jobs-cronjobs.html


```yaml
apiVersion: batch/v1beta1
kind: CronJob
metadata:
  name: whale-say-cronjob
spec:
  schedule: "*/1 * * * *" 
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: whale-say-container
            image: docker/whalesay
            command: ["cowsay","Hello DevNation"]
          restartPolicy: Never

```