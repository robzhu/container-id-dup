---
title: "Update IAM settings for your Workspace"
chapter: false
weight: 4
---

{{% notice info %}}
AWS Cloud9 normally manages IAM credentials dynamically. This isn't currently compatible with
the EKS IAM (Identity and Access Management) authentication, so we will disable it and rely on the IAM role instead.
{{% /notice %}}

- From within your workspace, click the sprocket, or launch a new tab to open the Preferences tab
- Select **AWS SETTINGS**
- Turn off **AWS managed temporary credentials**
- Close the Preferences tab
![c9disableiam](/images/c9disableiam.png)


#### Install command line tools 

Install command line tools by executing:

```bash 
~/environment/init_scripts/cli_tools.sh 
```
Make sure to reload bash profile

```bash 
source ~/.bash_profile
```

To ensure temporary credentials aren't already in place we will also remove
any existing credentials file:
```
rm -vf ${HOME}/.aws/credentials
```

We should configure our aws cli with our current region as default:
```
export ACCOUNT_ID=$(aws sts get-caller-identity --output text --query Account)
export AWS_REGION=$(curl -s 169.254.169.254/latest/dynamic/instance-identity/document | jq -r '.region')

echo "export ACCOUNT_ID=${ACCOUNT_ID}" >> ~/.bash_profile
echo "export AWS_REGION=${AWS_REGION}" >> ~/.bash_profile
aws configure set default.region ${AWS_REGION}
aws configure get default.region
```

### Validate the IAM role

Use the [GetCallerIdentity](https://docs.aws.amazon.com/cli/latest/reference/sts/get-caller-identity.html) CLI command to validate that the Cloud9 IDE is using the correct IAM role.

```
aws sts get-caller-identity

```

<!--
First, get the IAM role name from the AWS CLI.
```bash
INSTANCE_PROFILE_NAME=`basename $(aws ec2 describe-instances --filters Name=tag:Name,Values=aws-cloud9-${C9_PROJECT}-${C9_PID} | jq -r '.Reservations[0].Instances[0].IamInstanceProfile.Arn' | awk -F "/" "{print $2}")`
aws iam get-instance-profile --instance-profile-name $INSTANCE_PROFILE_NAME --query "InstanceProfile.Roles[0].RoleName" --output text
```
-->

The output assumed-role name should contain:
```
TeamRole
```

#### VALID

If the _Arn_ contains the role name from above and an Instance ID, you may proceed.

```output
{
    "Account": "123456789", 
    "UserId": "AROASZJSJAGFMMAJLP52A:MasterKey", 
    "Arn": "arn:aws:sts::191768363402:assumed-role/TeamRole/i-032qawsee877f6d01"
}
```
