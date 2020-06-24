
+++
title = "Kubernetes Basics"
date = 2019-10-28T11:40:22+11:00
weight = 1
+++

## Kubernetes Basics
Now let's focus on how we can use some simple commands and the Kubernetes command line tool **kubectl** to deploy containers to our **Amazon EKS cluster**.

{{% notice tip %}}
If you get an error when you try to run kubectl try re-running this commands: ```aws eks update-kubeconfig --name 'EKS-Lab'```
{{% /notice %}}

At the prompt type `kubectl version` to confirm that both the client and server are there and working.

        kubectl version

Next type `kubectl create deployment nginx --image=nginx`. This will create a single-[pod](https://kubernetes.io/docs/concepts/workloads/pods/pod/#what-is-a-pod) deployment of nginx.

        kubectl create deployment nginx --image=nginx

Use the command `kubectl describe deployments` to see the details of our new [deployment](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/).

        kubectl describe deployments

Not let's run `kubectl describe pods` to see the pod that our deployment has created for us. **Note that there is no port exposed for this contianer**

        kubectl describe pods

Running `kubectl describe replicasets` allows us to see that there is a replicaset too.

        kubectl describe replicasets

{{% notice info %}}
A `Deployment` creates/manages `ReplicaSet(s)` which, in turn, creates the required `Pod(s)`
{{% /notice %}}

Try typing `kubectl scale --replicas=3 deployment/nginx` to launch two more nginx Pods. Taking us to three.

        kubectl scale --replicas=3 deployment/nginx

You can run `kubectl get deployments` and `kubectl get pods -o wide` to see our change has taken effect (that there are three pods running). Also note the Pod IPs (copy/paste them to notepad or something).

        kubectl get deployments

        kubectl get pods -o wide

Type `kubectl run my-shell --rm -i --tty --image ubuntu -- bash` to connect interactively into `bash` on a new ubuntu pod.

        kubectl run my-shell --rm -i --tty --image ubuntu -- bash

Within the pod, run `apt update; apt install curl -y` then `curl http://<an IP from describe pods>` and watch it load the default nginx page on our nginx pod.

        apt update; apt install curl -y
        curl http://<an IP from describe pods>

{{% notice info %}}
    By default all pods in the cluster can reach all other pods in the cluster directly by IP. You can restrict this with `NetworkPolicies` which is Kubernetes' firewall.
{{% /notice %}}

Use the `exit` command, because we did a --rm in the command above, it'll delete the deployment and pod once we disconnect from our interactive session.

        exit

Next, lets try `kubectl expose deployment nginx --port=80 --target-port=80 --name nginx --type=LoadBalancer` to create a service backed by an [AWS Elastic Load Balancer](https://aws.amazon.com/elasticloadbalancing/) that not only balances the load between all the Pods but exposes it to the Internet.

        kubectl expose deployment nginx --port=80 --target-port=80 --name nginx --type=LoadBalancer

{{% notice info %}}
    It will take a minute to create the ELB. You can watch the progress of it being created in the AWS EC2 Console under `Load Balancers` on the left-hand side.
{{% /notice %}}

Run `kubectl get services` and copy the `EXTERNAL-IP` address. Open a web browser tab and go to `http://<that address>` and see it load. Note that it will take a minute or so for the ELB to be provisioned before this will work. Refresh this a few times to generate some access logs.

        kubectl get services

Now type `kubectl logs -lapp=nginx` to get the aggregated logs of all the nginx Pods to see the details of our recent requests.

        kubectl logs -lapp=nginx

We can also use `kubectl get service/nginx deployment/nginx --export=true -o yaml > nginx.yml` to back up our work.

        kubectl get service/nginx deployment/nginx --export=true -o yaml > nginx.yml

Edit the file and have a look (**double-click on it in Cloud9 in the pane on the left to open it in that IDE or use nano/vim in the Terminal**). We could have written our requirements for what we need Kubernetes to do into YAML files like this in the first place instead of using these kubectl commands - and often you would do that and put it in a CI/CD pipeline etc. Note that you can put the definitions for multiple types of resources (in this example both a service and a deployment) in one YAML file.

If you're feeling brave enter `kubectl delete service/nginx deployment/nginx` to clean up what we've done.

        kubectl delete service/nginx deployment/nginx

And for the final mic-drop, type `kubectl apply -f nginx.yml` to put it back again. **See how easy it is to export and reapply the YAML?**

        kubectl apply -f nginx.yml