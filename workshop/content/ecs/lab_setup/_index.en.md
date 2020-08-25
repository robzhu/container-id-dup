+++
title = "Setup"
date = 2019-10-28T15:13:26+11:00
weight = 2
+++

For this lab, we need to switch to the other **AWS Cloud9** workspace that was created. Let's get started by launching the **AWS Cloud9** console: [AWS Cloud9 console](https://console.aws.amazon.com/cloud9/)

1. Find the workspace named "Project-***STACK_NAME***":

    ![pick-your-idea](/images/01pickidea2.png)

    When you open the IDE, you'll be presented with a welcome screen that looks like this:

    ![cloud9-welcome](/images/00-cloud9-welcome.png)

2. Clone the Mythical Mysfits Workshop Repository:

    In the bottom panel of your new Cloud9 IDE, you will see a terminal command line terminal open and ready to use.  Run the following git command in the terminal to clone the necessary code to complete this tutorial:

    ```
    $ git clone https://github.com/aws-samples/amazon-ecs-mythicalmysfits-workshop.git
    ```

    After cloning the repository, you'll see that your project explorer now includes the files cloned.

    In the terminal, change directory to the subdirectory for this workshop in the repo:

    ```
    $ cd amazon-ecs-mythicalmysfits-workshop/workshop-1
    ```

3. Run some additional automated setup steps with the `setup` script:

    ```
    $ script/setup
    ```

    This script will delete some unneeded Docker images to free up disk space, populate a DynamoDB table with some seed data, upload site assets to S3, and install some Docker-related authentication mechanisms that will be discussed later. Make sure you see the "Success!" message when the script completes.

4. We should configure our aws cli with our current region as default:

    ```
    export ACCOUNT_ID=$(aws sts get-caller-identity --output text --query Account)
    export AWS_REGION=$(curl -s 169.254.169.254/latest/dynamic/instance-identity/document | jq -r '.region')

    echo "export ACCOUNT_ID=${ACCOUNT_ID}" >> ~/.bash_profile
    echo "export AWS_REGION=${AWS_REGION}" >> ~/.bash_profile
    aws configure set default.region ${AWS_REGION}
    aws configure get default.region
    ```

### Checkpoint:
At this point, the Mythical Mysfits website should be available at the static site endpoint for the S3 bucket created by CloudFormation. You can visit the site at <code>http://<b><i>BUCKET_NAME</i></b>.s3-website.<b><i>REGION</i></b>.amazonaws.com/</code>. For your convenience, we've created a link in the CloudFormation outputs tab in the console. Alternatively, you can find the ***BUCKET_NAME*** in the CloudFormation outputs saved in the file `workshop-1/cfn-outputs.json`. ***REGION*** should be the [code](https://docs.aws.amazon.com/general/latest/gr/rande.html#s3_region) for the region that you deployed your CloudFormation stack in (e.g. <i>us-west-2</i> for the Oregon region.) Check that you can view the site, but there won't be much content visible yet until we launch the Mythical Mysfits monolith service:

![initial website](/images/00-website.png)

[*^ back to top*](#monolith-to-microservices-with-docker-and-aws-fargate)
