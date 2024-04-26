---
title: "Lab 3a - Defend with Egress Policy"
chapter: true
weight: 8
---


In this lab, you will create the following tag-based egress policies to:

  * allow AWS Services to keep SSM connectivity
  * prevent social security information from being exported from one of the spoke instances. 
  * allow connection to approved github accounts only.

---
![alt Lab3a Architecture](/static/16-lab/lab3a_egress_architecture.png "Lab3a Architecture")


Two rules will be created in this lab:

  1. Allow AWS Services
  2. For all instances tagged as "prod":<br>
         - prevent a list of social security numbers being transferred out <br>
         - allow connection to github repository "valtix-security", but not other github repository.

Below is a diagram to show how policy is related to policy ruleset that is applied on the Gateways.

![Ruleset](/static/16-lab/ruleset.png)


## Procedure

1. To create tag-based policy we need first to tag our workloads properly. Let's go to the AWS console and add the tags to the spoke EC2 instances

   * Add a tag to the EC2 instance spoke-z1-app with key "**Category**" and value "**prod**"
   * Add a tag to the EC2 instance spoke-z2-app with key "**Category**" and value "**dev**"

::alert[Note: <br>If you go to *Discover -> Inventory -> Tags*, you'll notice there is a Tag Name "*Category*" that shows up. Multicloud Defense continuous discovery picked up the tags that were just created, in real-time. ]{type="info"}
  
2. Create an Address Object

	* Navigate to **Manage -> Security Policies -> Addresses**
	* Click **Create Address**.
	* Select **Src/Dest**.
	* Provide a name (e.g vm-tag-prod)
	* Select the object type as **User Defined Tag**
	* Under the Instances Tag table, select the key "**Category**" and value "**prod**".
	* Click **Save** to save the address object.
	* Click **Create Address**.
	* Select **Src/Dest**.
	* Provide a name (e.g AWS-Services).
	* Select the object type as **Service End Point (Cloud Service IP)**
	* Select the CSP Account that was onboarded in lab 1.
	* Select **AMAZON** for Cloud Service IP.
	* Select **US East (N. Virginia) us-east-1** for the Region.
	* Click **Save** to save the address object

3. Create Data Loss Protection Profile
	* Go to **Manage -> Profiles -> Network Threats**.
	* Click **Create Intrusion Profile**
	* Select **Data Loss Prevention**
	* Provide a name (e.g block_social_security)
	* In the DLP Filter List table, type US Social Security Number in the Patterns text column/field
	* Set 2 in the Count (sending more than 2 SSNs in the traffic would trigger the action)
	* Select **Deny Log** as the Action
	* **Save** the profile

4. Create URL Filtering Profile 
	* Navigate to **Manage -> Profiles -> URL Filtering**.
	* Click on **Create** button.
	* Provide a name for the URL profile. (eg. allow-valtix-security-github)
	* Fill in the following information:

     	Parameter | Value
     	----------|----------
     	URLs/Categories | http.\*github.com/valtix-security.\*
     	Methods | ALL
     	Policy | Allow Log

	* Add another URL list by clicking on the **Add** button within the same profile.
	* Fill in the following information in the new URL list entry:

     	Parameter | Value
     	----------|----------
     	URLs/Categories | http.\*github.com/.\*
     	Methods | ALL
     	Policy | Deny Log
     	Return Status Code | 502

	* Click **Save**

5. Create Policy in Ruleset. Now we have all the components to create a policy. 
	* Click **Manage -> Security Policies -> Rule Sets**
	* Click the "ciscomcd-sample-egress-policy-ruleset" ruleset.
	* Click **Add** to create a new rule.  A new rule editor opens in the slide over panel on the right
	* Fill in the following information:

     	Parameter| Value
     	---------|------
     	Name | Egress_prod
     	Type | Forward Proxy
     	Service | ciscomcd-sample-egress-forward-proxy
     	Source | vm-tag-prod
     	Destination | any
     	Action | Allow Log
     	Network Intrusion | ciscomcd-sample-ips-balanced-alert
     	Data Loss Prevention | block_social_security
     	URL Filtering | allow-valtix-security-github

	* Move the newly created rule above the ciscomcd-sample-egress-forwarding-allow-snat rule by dragging the rule to the top.
	* Click **Add** to create a new rule.  A new rule editor opens in the slide-over panel on the right
	* Fill in the following information:

     	Parameter| Value
     	---------|------
     	Name | AWS_Services
     	Type | Forwarding
     	Service | ciscomcd-sample-egress-forwarding-snat
     	Source | any-private-rfc1918
     	Destination | AWS-Services
     	Action | Allow Log

	* Move the newly created rule above the Egress_prod rule by dragging the rule to the top.
	* Click **Save Changes**.
<br><br>

::alert[**Policy Explanation:** <br>The policy that we just created will match all workloads that is tagged as "prod" and the policy will apply advanced security profiles(IPS, DLP, URL Filtering) on the session that is matched. All "prod" workloads can connect to github repository named "valtix-security" and no other URLs. ]{type="info"}
    

## Verification

1. SSH to the EC2 instance named **spoke-z1-app**
2. Execute :code[curl https://www.example.com -kv -d "6604-05-1120 604-05-1121" -o /dev/null]{showCopyAction=true}
3. Check that you get a 502 Bad Gateway error.

     ```
     * upload completely sent off: 24 out of 24 bytes
     < HTTP/1.1 502 Bad Gateway
     < Server: nginx/1.12.2
     < Date: Fri, 11 Feb 2022 02:11:36 GMT
     < Content-Type: text/html
     < Content-Length: 500
     < Connection: close
     < ETag: "61dfafc2-1f4"
     ```

4. Go to **Investigate -> Flow Analytics -> Network Threats**
5. You will note logs for the DLP dropped requests with a message: Sensitive Data was Transmitted Across the Network
6. Download a file from valtix-security repository on spoke1-vpc. :code[curl -o test.zip -kv https://github.com/valtix-security/tutorials/raw/main/test.zip]{showCopyAction=true}. This connection should be allowed.
7. Download a file from a different github account. eg :code[curl -o test_file.txt -kv https://github.com/michaelvaltix/tutorials/blob/main/test_file.txt]{showCopyAction=true}. This connection should be denied.
8. Navigate to **Investigate -> Flow Analytics -> URL Filtering**.
9. You should see both the allow session and the deny session for the 2 curl from github.
10. Notice that we did not specify any IP address in the policy, but the vm instance still matches the policy. This is because of the tag-based object that we used in the policy. This policy will be applied to any instance with the tag **prod**. This allows for the policy to be dynamic. Future instances that is considered as prod environment will by this rule applied simply by adding tag value {Category: prod}  
11. Repeat the verification steps for spoke-z2-app. You will notice that all traffic passes because it is matches different policy. It matches the "ciscomcd-sample-egress-forwarding-allow" policy rather than the policy we created because spoke-z2-app does not have the correct tag. 

