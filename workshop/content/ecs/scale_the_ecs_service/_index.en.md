+++
title = "Scale the adoption platform monolith with an ALB and an ECS Service"
date = 2019-10-28T15:13:26+11:00
weight = 5
+++

The Run Task method you used in the last lab is good for testing, but we need to run the adoption platform as a long running process.

In this lab, you will use an Elastic Load Balancing [Appliction Load Balancer (ALB)](https://aws.amazon.com/elasticloadbalancing/) to distribute incoming requests to your running containers. In addition to simple load balancing, this provieds capabilities like path-based routing to different services.

What ties this all together is an **ECS Service**, which maintains a desired task count (i.e. n number of containers as long running processes) and integrates with the ALB (i.e. handles registration/deregistration of containers to the ALB). An initial ECS service and ALB were created for you by CloudFormation at the beginning of the workshop. In this lab, you'll update those resources to host the containerized monolith service. Later, you'll make a new service from scratch once we break apart the monolith.

![Lab 3 Architecture](/images/03-arch.png)


### Instructions:

1. Test the placeholder service:

    The CloudFormation stack you launched at the beginning of the workshop included an ALB in front of a placeholder ECS service running a simple container with the NGINX web server. Find the hostname for this ALB in the "LoadBalancerDNS" output variable in the `cfn-output.json` file, and verify that you can load the NGINX default page:

    ![NGINX default page](/images/03-nginx.png)

3. Update the service to use your task definition:

    Find the ECS cluster named <code>Cluster-<i><b>STACK_NAME</b></i></code>, then select the service named <code><b><i>STACK_NAME</i></b>-MythicalMonolithService-XXX</code> and click "Update" in the upper right:

    ![update service](/images/03-update-service.png)

    Update the Task Definition to the revision you created in the previous lab, then click through the rest of the screens and update the service.

4. Test the functionality of the website:

    You can monitor the progress of the deployment on the "Tasks" tab of the service page:

    ![monitoring the update](/images/03-deployment.png)

    The update is fully deployed once there is just one instance of the Task running the latest revision:

    ![fully deployed](/images/03-fully-deployed.png)

    Visit the S3 static site for the Mythical Mysfits (which was empty earlier) and you should now see the page filled with Mysfits once your update is fully deployed. Remember you can access the website at <code>http://<b><i>BUCKET_NAME</i></b>.s3-website.<b><i>REGION</i></b>.amazonaws.com/</code> where the bucket name can be found in the `workshop-1/cfn-output.json` file:

    ![the functional website](/images/03-website.png)

    Click the heart icon to like a Mysfit, then click the Mysfit to see a detailed profile, and ensure that the like count has incremented:

    ![like functionality](/images/03-like-count.png)

    This ensures that the monolith can read from and write to DynamoDB, and that it can process likes. Check the CloudWatch logs from ECS and ensure that you can see the "Like processed." message in the logs:

    ![like logs](/images/03-like-processed.png)

<details>
<summary>INFO: What is a service and how does it differ from a task??</summary>

An [ECS service](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs_services.html) is a concept where ECS allows you to run and maintain a specified number (the "desired count") of instances of a task definition simultaneously in an ECS cluster.


tl;dr a **Service** is comprised of multiple **tasks** and will keep them up and running. See the link above for more detail.

</details>

### Checkpoint:
Sweet! Now you have a load-balanced ECS service managing your containerized Mythical Mysfits application. It's still a single monolith container, but we'll work on breaking it down next.

[*^ back to the top*](#monolith-to-microservices-with-docker-and-aws-fargate)
