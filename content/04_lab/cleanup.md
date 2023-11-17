---
title: "Clean Up"
chapter: true
weight: 9
---

# Clean Up


1. Navigate to **Manage -> Gateways -> Gateways**.
2. Checkbox the Gateway created in Lab 2.
3. Click **Disable**.
4. Once the status shows as **INACTIVE**, click on **Delete**. Wait for Gateway to be removed from the table
5. Navigate to **Manage -> Gateways -> Service VPCs**.
6. Checkbox Service VPC created in Lab 2 and click **Delete**. Wait for Service VPC to be removed from the table.
7. Navigate to **Manage -> Cloud Accounts -> Accounts**
8. Checkbox the AWS account onboarded in LAB 1.
9. Click on **Delete**.
10. Login to AWS console and go to S3 service.
11. Manually delete the S3 bucket created by Valtix CloudFormation template.
12. Go to CloudFormation. 
13. Find the CloudFormation stacks created used in this workshop. Click on Delete. 
Note: There are 2 CloudFormation stack deployed in this workshop: 
  * CFT in prerequisite used as spoke 
  * CFT used to deploy in Lab 1 to onboard AWS account 

