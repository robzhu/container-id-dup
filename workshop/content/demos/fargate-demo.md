+++
title = "Deploying Fargate using the Cloud Development Kit (CDK)"
date = 2019-10-28T11:40:22+11:00
weight = 2
+++

Install the AWS CDK using the following command.

        npm install -g aws-cdk

Run the following command to see the version number of the AWS CDK.

        cdk --version

If you get an error message that your language framework is out of date, use one of the following commands to update the components that the AWS CDK needs to support the language.

        npx npm-check-updates -u

Clone the aws-cdk-nyan-cap sample application

        git clone git@github.com:anz-containers/aws-cdk-nyan-cat.git

Switch to the aws-cdk-nyan-cat directory

        cd aws-cdk-nyan-cat
