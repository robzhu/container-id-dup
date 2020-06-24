---
title: "Create IAM Role"
date: 2018-10-087T08:30:11-07:00
weight: 10
draft: false
---

In an AWS CodePipeline, we are going to use AWS CodeBuild to deploy a sample Kubernetes service.
This requires an [AWS Identity and Access Management](https://aws.amazon.com/iam/) (IAM) role capable of interacting
with the EKS cluster.

We have already defined a role for CodeBuild and added the role to the [aws-auth ConfigMap](https://docs.aws.amazon.com/eks/latest/userguide/add-user-role.html)
for the EKS cluster.
