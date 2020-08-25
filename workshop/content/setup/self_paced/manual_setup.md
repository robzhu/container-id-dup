+++
title = "Deploy the CloudFormation Stack for AWS Container Immersion Day"
date = 2019-10-28T11:40:22+11:00
weight = 2
+++

<center>

{{% button href="https://console.aws.amazon.com/cloudformation/home?#/stacks/new?stackName=containerday&templateURL=https://anz-container-day.s3-ap-southeast-2.amazonaws.com/templates/containerday.yaml" %}}Setup the AWS Container Immersion Day Labs for Me!{{% /button %}}

</center>

{{% notice note %}}
Accept all of the defaults and be sure to check **I acknowledge that AWS CloudFormation might create IAM resources**.
{{% /notice %}}

Once the stack has been deployed finish off the setup by configuring the IAM settings for the [AWS Cloud9 Workspace](../../workspaceiam)