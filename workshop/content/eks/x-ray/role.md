---
title: "Modify IAM Role"
date: 2018-11-177T08:30:11-07:00
weight: 10
draft: false
---

In order for the [X-Ray daemon](https://docs.aws.amazon.com/xray/latest/devguide/xray-daemon.html) 
to communicate with the service, we need to create a Kubernetes [service account](https://aws.amazon.com/blogs/opensource/introducing-fine-grained-iam-roles-service-accounts/) and attach an [AWS Identity and Access Management](https://aws.amazon.com/iam/) (IAM) role and policy with sufficient permissions.

Create the service account for X-Ray.

```bash
eksctl create iamserviceaccount --name xray-daemon --namespace default --cluster EKS-Lab --attach-policy-arn arn:aws:iam::aws:policy/AWSXRayDaemonWriteAccess --approve --override-existing-serviceaccounts
```

Apply a label to the service account

```bash
kubectl label serviceaccount xray-daemon app=xray-daemon
```