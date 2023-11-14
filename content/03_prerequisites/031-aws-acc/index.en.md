---
chapter: false
title: AWS Account
weight: 31
---

::alert[Your account must have the ability to create new IAM roles and scope other IAM permissions.]{type="warning"}

You may complete these labs with your own AWS account. Make sure that you have the proper IAM permissions to create new IAM roles and scope other needed IAM permissions. No additional setup is required.


---

1. If you don't already have an AWS account with Administrator access: [Create one now by clicking here](https://aws.amazon.com/getting-started/)

2. Once you have an AWS account, ensure you are following the remaining workshop steps
as an IAM user with administrator access to the AWS account:
[Create a new IAM user to use for the workshop](https://console.aws.amazon.com/iam/home?#/users$new)

3. Login to the AWS Console an create a new IAM user following the steps shown below:
![Create User](/static/15-prerequisites/self_paced/iam-1-create-user.png)

4. Attach the AdministratorAccess IAM Policy:
![Attach Policy](/static/15-prerequisites/self_paced/iam-2-attach-policy.png)

::alert[Please note that in practice IAM policies should adhere to the least privilege principle.]{type="info"}

5. Click to create the new user:
![Confirm User](/static/15-prerequisites/self_paced/iam-3-create-user.png)

6. Take note of the login URL and save:
![Login URL](/static/15-prerequisites/self_paced/iam-4-save-url.png)
