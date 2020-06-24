---
title: "Configure Cluster Autoscaler (CA)"
date: 2018-08-07T08:30:11-07:00
weight: 30
---
Cluster Autoscaler for AWS provides integration with Auto Scaling groups. It enables users to choose from four different options of deployment:

* **One Auto Scaling group** - This is what we will use
* Multiple Auto Scaling groups
* Auto-Discovery
* Master Node setup

### Configure the Cluster Autoscaler (CA)
We have provided a manifest file to deploy the CA. Copy the commands below into your Cloud9 Terminal.

```
mkdir ~/environment/cluster-autoscaler
cd ~/environment/cluster-autoscaler
wget https://raw.githubusercontent.com/kubernetes/autoscaler/master/cluster-autoscaler/cloudprovider/aws/examples/cluster-autoscaler-autodiscover.yaml
```

#### Configure the ASG

Using the file browser on the left, open cluster-autoscaler-autodiscover.yaml. Search for `name: cluster-autoscaler` and add cluster-autoscaler.kubernetes.io/safe-to-evict annotation to the deployment

```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: cluster-autoscaler
  namespace: kube-system
  annotations:
    cluster-autoscaler.kubernetes.io/safe-to-evict: 'false'
```

Next Search for `command:` and within this block replace <YOUR CLUSTER NAME> with your cluster's name, add the --balance-similar-node-groups  and --skip-nodes-with-system-pods=false options. Also  set cluster-autoscaler image tag to **1.14.6**

```bash 
        - image: k8s.gcr.io/cluster-autoscaler:v1.14.6
          name: cluster-autoscaler
          resources:
            limits:
              cpu: 100m
              memory: 300Mi
            requests:
              cpu: 100m
              memory: 300Mi
          command:
            - ./cluster-autoscaler
            - --v=4
            - --stderrthreshold=info
            - --cloud-provider=aws
            - --skip-nodes-with-local-storage=false
            - --expander=least-waste
            - --node-group-auto-discovery=asg:tag=k8s.io/cluster-autoscaler/enabled,k8s.io/cluster-autoscaler/EKS-Lab
            - --balance-similar-node-groups
            - --skip-nodes-with-system-pods=false

```


Although Cluster Autoscaler is the de facto standard for automatic scaling in K8s, it is not part of the main release. We deploy it like any other pod in the kube-system namespace, similar to other management pods.


### Deploy the Cluster Autoscaler

```
kubectl apply -f ~/environment/cluster-autoscaler/cluster-autoscaler-autodiscover.yaml
```

Watch the logs
```
kubectl logs -f deployment/cluster-autoscaler -n kube-system
```

#### We are now ready to scale our cluster

{{%attachments title="Related files" pattern=".yaml"/%}}
