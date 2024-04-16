---
date: "2023-Nov-13"
chapter: false
title: Deploying Sample Application
weight: 34
---

This phase of the lab guide involves launching a demo application for testing, ensuring it doesn't interfere with ongoing operations with you existing workloads. If you'd like to proceed with the workshop using your current workload, this phase can be omitted.

## Deploying Sample Application

To complete this lab, you will need an application/instance for Multicloud Defense to protect. If you don't have a readily avaliable application/instance, follow these steps to deploy a sampe application for this workshop. 

1. EC2 key pairs in **us-east-1** region

     * Navigate to **EC2 service** and under **Network & Security -> Key Pairs**, create a new key pair.
     * Provide a Name for the key pair. If you are using putty on Windows, select .ppk for the Private key file format. If using Mac/Linux, use .pem format.
     * You may need to change the permission of the downloaded private key. `chmod 400 <private key>.pem`
 
2. EC2 instances in the AWS account. These ec2 instances will be used to generate traffic and show the capabilities of Multicloud Defense. For this workshop, use the following [CloudFormation template](https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/quickcreate?templateUrl=https%3A%2F%2Fvaltix-public.s3.amazonaws.com%2Fcloud-formation%2Faws-dev-day.yml&stackName=spoke1-vpc&param_AppAMI=valtix-default&param_BastionHost=no&param_InstanceType=t3a.small&param_KeyPairName=&param_Prefix=spoke1&param_SubnetBits=8&param_VPCCidr=10.0.0.0%2F16&param_ValtixResources=no&param_Zone1=us-east-1a&param_Zone2=us-east-1b) and deploy in AWS account.
    
     * This CloudFormation template will deploys two ec2 instances in two subnets inside one VPC. 
     * Below are the parameters needed for CloudFormation Template. Most parameters can be left as default. EC2 Key Pair is the option that the user needs to fill in. 

        Parameter | Description
        ----------|-------------
        Stack name| Name of the CloudFormation Stack.
        Prefix | A prefix that is used in the resource name
        VPC CIDR | The CIDR Block for VPC
        Subnet Bits | Number of bits for subnet.
        Availability Zone 1| Availability Zone to deploy the first EC2 instance
        Availability Zone 2| Availability Zone to deploy the second EC2 instance
        AMI for App Instance| AMI to use for the EC2 instance.
        Instance Type | Instance Type to use for EC2 instance
        EC2 Key Pair | The EC2 key pair to used for EC2 instance.
     
     * This would be referred to as spoke deployment or spoke1-vpc. If you want to use your own ec2 instances for this workshop, whenever there is reference to spoke, you can use your own ec2 instances.


3. A login to [Multicloud Defense Controller Portal](https://www.defenseorchestrator.com/). To obtain an account to Multicloud Defense Controller, please see section: Obtaining Cisco Tenant 




