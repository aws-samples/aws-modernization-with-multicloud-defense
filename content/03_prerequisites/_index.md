---
title: "Prerequisites"
chapter: true
weight: 3
---

# Prerequisites

- This lab is assumed to be run on region **us-east-1**
- A login to [Valtix Controller Portal](https://prod1-dashboard.vtxsecurityservices.com/). To obtain an account to Valtix Controller, sign up for our [Free Tier](https://valtix.com/sign-up/)
- AWS account to secure.
- EC2 key pairs in **us-east-1** region
- EC2 instances in the AWS account. These ec2 instances will be used to generate traffic and show the capabilities of Valtix. For this workshop, use the following [CloudFormation template](https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/quickcreate?templateUrl=https%3A%2F%2Fvaltix-public.s3.amazonaws.com%2Fcloud-formation%2Fvaltix-datapath.yml&stackName=spoke1-vpc&param_AppAMI=valtix-default&param_BastionHost=no&param_InstanceType=t3a.small&param_KeyPairName=&param_Prefix=spoke1&param_SubnetBits=8&param_VPCCidr=10.0.0.0%2F16&param_ValtixResources=no&param_Zone1=us-east-1a&param_Zone2=us-east-1b) and deploy in AWS account. This CloudFormation template will deploys two ec2 instances in two subnet inside one VPC. The only field user needs to input is the SSH key pair. The rest you can leave as default. This would be referred to as spoke deployment or spoke-vpc. If you want to use your own ec2 instances for this workshop, whenever there is reference to spoke, you can use your own ec2 instances.


