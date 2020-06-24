+++
title = "Deploying an Amazon EKS Cluster using EKSCTL"
date = 2019-10-28T11:40:22+11:00
weight = 1
+++

Install Kubernetes CLI and eksctl using homebrew.

        $ brew tap weaveworks/tap 
        $ brew install kubernetes-cli kubernetes-helm weaveworks/tap/eksctl

Create a config file for our cluster (replace the region with yours)

    $ cat << EOF > cluster.yaml
    apiVersion: eksctl.io/v1alpha5
    kind: ClusterConfig

    metadata:
    name: cluster
    region: ap-southeast-2
    version: "1.14"

    vpc:
    subnets:
        private:
        us-west-2a:
            id: "Quick-Start-VPC-PrivateSubnet1AID"
        us-west-2b:
            id: "Quick-Start-VPC-PrivateSubnet2AID"
        us-west-2c:
            id: "Quick-Start-VPC-PrivateSubnet3AID"
        public:
        us-west-2a:
            id: "Quick-Start-VPC-PublicSubnet1ID"
        us-west-2b:
            id: "Quick-Start-VPC-PublicSubnet2ID"
        us-west-2c:
            id: "Quick-Start-VPC-PublicSubnet3ID"

    cloudWatch:
        clusterLogging:
            enableTypes:
                - "audit"
                - "authenticator"
                - "api"
                - "controllerManager"
                - "scheduler"

    nodeGroups:
    - name: ng
        instanceType: m5.xlarge
        desiredCapacity: 1
        privateNetworking: true
        volumeSize: 100
        volumeType: gp2
        volumeEncrypted: true
        ssh:
        publicKeyPath: ~/.ssh/id_rsa.pub
        iam:
        attachPolicyARNs:
            - arn:aws:iam::aws:policy/AmazonEKSWorkerNodePolicy
            - arn:aws:iam::aws:policy/AmazonEKS_CNI_Policy
        withAddonPolicies:
            imageBuilder: true
            autoScaler: true
            externalDNS: true
            certManager: true
            appMesh: true
            ebs: true
            fsx: true
            efs: true
            albIngress: true
            xRay: true
            cloudWatch: true
    EOF

... now deploy the cluster

        $ eksctl create cluster -f cluster.yaml

{{%expand "Expand to see more details about the cluster.yaml parameters"%}}
With eksctl you can either specify the parameters at runtime on the commandline or specify them in a YAML file that is actually quite similar in syntax to Kubernetes YAML files.

The cluster.yaml file that we included has the following options:

* Specified the version of the cluster as being specific here really helps with GitOps and making sure that we are explicit/deliberate about our versions and their upgrades.

* Specified the details of the VPC and subnets that we want to build the cluster in. While having eksctl create one for each cluster is good for demos in many cases you'll want to put your clusters into existing networks and this is how to do that.

* Enable Logging - it is important from a security perspective to at least enable the audit logging from the control plane to CloudWatch Logs. Instead of saying "*" or "all" in the example we listed all the options here so you can remove any that you don't feel you need to save CloudWatch Logs cost.

* We can specify one or more NodeGroups which are EC2 AutoScaling Groups (ASGs) for our worker nodes. The options include:
    
    * The instance type/size we want to use
    
    * The region to deploy it to
    
    * The initial desiredCapacity of the ASG (can be changed by things like the Cluster Autoscaler later)
    
    * If these nodes are going to be put in a private subnet instead of a public one you need to say privateNetworking = true
    
    * The size and type of the EBS volume for each node. This is where the cached docker images and any Volumes will be stored so making it large enough to accomodate them is important.
    
    * Whether to encrypt those volumes - there is no reason not to do this cost or performance-wise and it will let you be able to say "yes it is encrytped" if your security team ever asks.
    
    * If you want to be able to ssh to these nodes you need to specify the key. You can either specify a key that already has been imported or give it a key file and it'll import that to EC2 for you first.
    
    * The IAM policies for the role assigned to each node:
        
        * In addition to specifying specific policy ARNs there are a number of built-in Addon Policies you can name eksctl knows how to create. I listed all of them here and you can remove any that you don't need for least privilege.
{{ /%expand%}}



