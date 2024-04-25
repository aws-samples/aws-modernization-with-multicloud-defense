---
title: "Deploy Centralized East-West"
chapter: true
weight: 9
---

::alert[The steps to "Deploy Centralized Egress" and this lab section are the same. If you have already gone through "Deploy Centralized Egress," you can skip to "Defending with East-West" section.]{type="info"}


In Lab 1, the enablement of Multicloud Defense’s discovery feature provided an inventory of the account and what traffic type was in the network. In a single click, you can see if any instances are potentially connecting to a malicious destination.  We will secure the network in this lab by deploying a Service VPC with Multicloud Defense Gateway in a hub-n-spoke model. Below is what we will achieve after this lab.

![Centralized_VPC](/static/16-lab/centralized_model.png)

## Procedure

If you have completed lab "Deploy Centralized Egress," skip to step 8.
1. Navigate to **Easy Setup -> Service VPC**.
2. Fill in all the information:

     Parameter | Description
     ----------|-------------
     Name| Provide a name for Service VPC
     CSP Account| Select the account that was onboarded in Lab1.
     Region| Select the region where you deployed your CFT. 
     CIDR Block| Provide any /16 subnet. Example 10.100.0.0/16
     Availability Zones| It is recommended that you select two AZs for redundancy.
     Transit Gateway| Select “create-new”
     Transit Gateway Name|  Provide a name for Transit Gateway. Example mcd-workshop-tgw.
     Auto Accept shared attachments| Leave it unchecked.
     Use NAT Gateway| Leave it unchecked.

3. Click on **Save & Continue**. This process may take 5 minutes. During this time, please do not navigate to a different link.
4. After completing the deployment of Service VPC, you should be taken to **Create Gateway page** where Multicloud Defense will orchestrate the deployment of the Multicloud Defense Gateway in the Service VPC created.
5. Fill in the information to create Multicloud Defense Gateway:

     Parameter | Description
     ---------|-------------
     Account| Select the account that was onboarded in Lab1.
     Service VPC| Select the Service VPC that was created in step 4.
      Gateways| Check only “East-West & Egress”.
     East-West & Egress Gateway Name| Provide a name for the East-West & Egress Gateway. Example: aws-workshop-gw.
     East-West & Egress Gateway Policy Ruleset| Leave it as default, ciscomcd-sample-egress-policy-ruleset.
     Gateway IAM Role Name| This value is from the CFT Outputs, use the value of "MCDFirewallRoleName" (e.g ciscomcd-firewall-role)
     SSH Key Pair| Select any SSH key pair you want to use. 
     
6. Click **Save & Continue**
7. You will be landed on the inventory page(**Manage -> Cloud Accounts -> Inventory**). Click on VPCs/VNets. 

![VPC](/static/16-lab/Inventory_VPC.png)

8. A list of all the VPCs in your account is shown here. This table will indicate whether the VPC is secured by Multicloud Defense. Find the spoke VPC that was deployed in the pre-requisite section.

9. Click on **Secure** button for **ciscomcd-vpc** and **ciscomcd-vpc2** and select the Service VPC that was created in step 3. Note: If you already completed "Deploy Centralized Egress," ciscomcd-vpc entry would have been protected. 

![secure](/static/16-lab/secure.png)

10. Checkbox all route table entries in the Route Table section. This will change the subnet route table to point to Transit Gateway. 

**Note:** you may lose connection to the sample ec2 instance after changing the default route. Reconnect to your sample ec2 instance.


## Verification 

1. Navigate to **Manage -> Gateways -> Service VPCs**
2. Verify the Service VPC exists in the table. Check that the State is **ACTIVE**
3. Navigate to **Manage -> Gateways -> Gateways**.
4. Check the Gateway, shown in the table and has the status **ACTIVE**.
5. From the EC2 instance, generate traffic to Google and Facebook.

    ```
    curl http://www.google.com
    curl http://www.facebook.com
    ```
    
6. Navigate to **Investigate -> Flow Analytics -> Traffic Summary**. This gives an overview of traffic inspected by Multicloud Defense Gateway.
7. Click on Logs. You should see your sessions on Google and Facebook in the Logs table.

