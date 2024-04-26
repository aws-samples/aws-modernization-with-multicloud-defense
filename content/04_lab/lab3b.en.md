---
title: "Lab 3b - Defending with East-West Policy"
chapter: true
weight: 9
---


In this lab, you will create a tag-based policies to:

  * Create tag-based policy to allow/deny east-west traffic

---
![alt Lab3b Architecture](/static/16-lab/lab3b_east_west_architecture.png "Lab3b Architecture")

Two rules will be created in this lab to prevent/allow east-west traffic:

  1. Only instances tagged as "**prod**" can talk to "**prod**"
  2. Deny "**dev**" to "**prod**"
         
Below is a diagram to show how policy is related to policy ruleset that is applied on the Gateways.

![Ruleset](/static/16-lab/ruleset.png)


## Procedure

1. To create a tag-based policy we need first to tag our workloads properly. Let's go to the AWS console and add the tags to the spoke EC2 instances

   * Add a tag to the EC2 instance spoke-z1-app with key "**Environment**" and value "**prod**"
   * Add a tag to the EC2 instance spoke-z2-app with key "**Environment**" and value "**dev**"
   * Add a tag to the EC2 instance spoke-z1-app2 with key "**Environment**" and value "**prod**"
   * Add a tag to the EC2 instance spoke-z2-app2 with key "**Environment**" and value "**dev**"

     ---
     ::alert[**Note:**<br> If you go to *Discover -> Inventory -> Tags*, you'll notice that a Tag Name "*Environment*" shows up. Multicloud Defense continuously discovered the tags that had just been created in real-time.]{type="info"}
     ---
2. Create an Address Object

	* Navigate to **Manage -> Security Policies -> Addresses**
	* Click **Create Address**.
	* Select **Src/Dest**.
	* Provide a name (e.g vm-tag-prod)
	* Select the object type as **User Defined Tag**
	* Under the Instances Tag table, select the key "**Environment**" and value "**prod**".
	* Click **Save** to save the address object.
	* Click **Create Address**.
	* Select **Src/Dest**.
	* Provide a name (e.g vm-tag-dev).
	* Select the object type as **User Defined Tag**
	* Under the Instances Tag table, select the key "**Environment**" and value "**dev**".
	* Click **Save** to save the address object.

3. Create Policy in Ruleset. Now we have all the components to create a policy. 
	* Click **Manage -> Security Policies -> Rule Sets**
	* Click the "ciscomcd-sample-egress-policy-ruleset" ruleset.
	* Click **Add** to create a new rule.  A new rule editor opens in the slide-over panel on the right
	* Fill in the following information:

     	Parameter| Value
     	---------|------
     	Name | East-West_prod_dev
     	Type | Forwarding
     	Service | ciscomcd-sample-egress-forwarding-snat
     	Source | vm-tag-prod
     	Destination | vm-tag-dev
     	Action | Deny Log
     	

	* Move the newly created rule above the ciscomcd-sample-egress-forwarding-allow-snat rule by dragging the rule to the top.
	* Click **Add** to create a new rule.  A new rule editor opens in the slide-over panel on the right
	* Fill in the following information:

     	Parameter| Value
     	---------|------
     	Name | East-West_prod_prod
     	Type | Forwarding
     	Service | ciscomcd-sample-egress-forwarding-snat
     	Source | vm-tag-prod
     	Destination | vm-tag-prod
     	Action | Allow Log

	* Move the newly created rule above the East_West_prod_dev rule by dragging the rule to the top.
	* Click **Save Changes**.
<br><br>

      --- 
::alert[Policy Explanation: <br>The policy that we just created will match all workloads that are tagged as "prod," and the policy will apply advanced security profiles(IPS, DLP, URL Filtering) on the session that is matched. All "prod" workloads can connect to the GitHub repository named "valtix-security" and no other URLs. ]{type="info"}
     
    

## Verification

1. SSH to the EC2 instance named **spoke-z1-app**
2. Perform a curl from spoke-z1-app to spoke-z1-app2. This should work.
3. Perform a curl fromspoke-z1-app to spoke-z2-app2. This should be denied.

