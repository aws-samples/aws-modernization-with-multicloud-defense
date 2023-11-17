---
title: "Lab 3: Defend"
chapter: true
weight: 8
---


In this lab, you will create a tag-based policies to:

  * allow AWS Services to keep SSM connectivity
  * prevent social security information from being exported from one of the spoke instances. 
  * allow connection to approved github accounts only.


Two rules will be created in this lab:

  1. Allow AWS Services
  2. For all instances tagged as "prod":<br>
         - prevent a list of social security numbers being transferred out <br>
         - allow connection to github repository "valtix-security", but not other github repository.

Below is a diagram to show how policy is related to policy ruleset that is applied on the Gateways.

![Ruleset](/static/16-lab/ruleset.png)


## Procedure

1. To create tag-based policy we need to first tag our workloads properly. Let's go to the AWS console and add the tags to the spoke EC2 instances

   * Add a tag to the EC2 instance spoke-z1-app with key "**Category**" and value "**prod**"
   * Add a tag to the EC2 instance spoke-z2-app with key "**Category**" and value "**dev**"

     ---
     **Note:** 
     If you go to *Discover -> Inventory -> Tags*, you'll notice there is a Tag Name "*Category*" that shows up. Valtix continuous discovery picked up the tags that was just created, in real-time
     ---
2. Create Address Object

	1. Navigate to **Manage -> Security Policies -> Addresses**
	2. Click **Create Address**.
	3. Select **Src/Dest**.
	4. Provide a name (e.g vm-tag-prod)
	5. Select the object type as **User Defined Tag**
	6. Under the Instances Tag table, select the key "**Category**" and value "**prod**".
	7. Click **Save** to save the address object.
	8. Click **Create Address**.
	9. Select **Src/Dest**.
	10. Provide a name (e.g AWS-Services).
	11. Select the object type as **Service End Point (Cloud Service IP)**
	12. Select the CSP Account that was onboarded in lab 1.
	13. Select **AMAZON** for Cloud Service IP.
	14. Select **US East (N. Virginia) us-east-1** for the Region.
	13. Click **Save** to save the address object

3. Create Data Loss Protection Profile
	1. Go to **Manage -> Profiles -> Network Threats**.
	2. Click **Create Intrusion Profile**
	3. Select **Data Loss Prevention**
	4. Provide a name (e.g block_social_security)
	5. In the DLP Filter List table, type US Social Security Number in the Patterns text column/field
	6. Set 2 in the Count (sending more than 2 SSNs in the traffic would trigger the action)
	7. Select **Deny Log** as the Action
	8. **Save** the profile

4. Create URL Filtering Profile 
	1. Navigate to **Manage -> Profiles -> URL Filtering**.
	2. Click on **Create** button.
	3. Provide a name for the URL profile. (eg. allow-valtix-security-github)
	4. Fill in the following information:

     	Parameter | Value
     	----------|----------
     	URLs/Categories | http.\*github.com/valtix-security.\*
     	Methods | ALL
     	Policy | Allow Log

	5. Add another URL list by clicking on the **Add** button within the same profile.
	6. Fill in the following information in the new URL list entry:

     	Parameter | Value
     	----------|----------
     	URLs/Categories | http.\*github.com/.\*
     	Methods | ALL
     	Policy | Deny Log
     	Return Status Code | 502

	7. Click **Save**

5. Create Policy in Ruleset. Now we have all the components to create a policy. 
	1. Click **Manage -> Security Policies -> Rule Sets**
	2. Click the "valtix-sample-egress-policy-ruleset" ruleset.
	3. Click **Add** to create a new rule.  A new rule editor opens in the slide over panel on the right
	4. Fill in the following information:

     	Parameter| Value
     	---------|------
     	Name | Egress_prod
     	Type | Forward Proxy
     	Service | valtix-sample-egress-forward-proxy
     	Source | vm-tag-prod
     	Destination | any
     	Action | Allow Log
     	Network Intrusion | valtix-sample-ips-balanced-alert
     	Data Loss Prevention | block_social_security
     	URL Filtering | allow-valtix-security-github

	5. Move the newly created rule above the valtix-sample-egress-forwarding-allow-snat rule by dragging the rule to the top.
	6. Click **Add** to create a new rule.  A new rule editor opens in the slide over panel on the right
	7. Fill in the following information:

     	Parameter| Value
     	---------|------
     	Name | AWS_Services
     	Type | Forwarding
     	Service | valtix-sample-egress-forwarding-snat
     	Source | any-private-rfc1918
     	Destination | AWS-Services
     	Action | Allow Log

	8. Move the newly created rule above the Egress_prod rule by dragging the rule to the top.
	9. Click **Save Changes**.
<br><br>

      --- 
      **Policy Explanation:**<br>
      The policy that we just created will match all workloads that is tagged as "prod" and the policy will apply advanced security profiles(IPS, DLP, URL Filtering) on the session that is matched. All "prod" workloads can connect to github repository named "valtix-security" and no other URLs. 
      ---
    

## Verification

1. SSH to the EC2 instance named **spoke-z1-app**
2. Execute `curl https://www.example.com -kv -d "6604-05-1120 604-05-1121" -o /dev/null`
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
6. Download a file from valtix-security repository on spoke1-vpc. `curl -o test.zip -kv https://github.com/valtix-security/tutorials/raw/main/test.zip`. This connection should be allowed.
7. Download a file from a different github account. eg `curl -o test_file.txt -kv https://github.com/michaelvaltix/tutorials/blob/main/test_file.txt`. This connection should be denied.
8. Navigate to **Investigate -> Flow Analytics -> URL Filtering**.
9. You should see both the allow session and the deny session for the 2 curl from github.
10. Notice that we did not specify any IP address in the policy, but the vm instance still matches the policy. This is because of the tag-based object that we used in the policy. This policy will be applied to any instance that has the tag **prod**. This allows for the policy to be dynamic. Future instances that is considered as prod environment will by this rule applied simply by adding tag value {Category: prod}  
11. Repeat the verification steps for spoke-z2-app. You will notice that all traffic passes because it is matching a different policy. It is matching "valtix-sample-egress-forwarding-allow" policy rather than the policy we created because spoke-z2-app does not have the correct tag. 

