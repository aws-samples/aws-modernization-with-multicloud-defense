---
title: "Lab 3: Defend"
chapter: true
weight: 7
---

# Lab 3: Defend

In this lab, you will create a policy to:
  * prevent social security information from being exported from one of the spoke instances. 
  * allow connection to approved github accounts only.

## Procedure

1. Go to the AWS console and add the tags to the spoke EC2 instances

   * Add a tag to the EC2 instance spoke-z1-app with key "**Category**" and value "**prod**"
   * Add a tag to the EC2 instance spoke-z2-app with key "**Category**" and value "**dev**"

2. Navigate to **Manage -> Security Policies -> Addresses**
3. Click **Create Address**.
4. Select **Src/Dest**.
5. Provide a name (e.g vm-tag-dev)
6. Select the object type as **User Defined Tag**
7. Under the Instances Tag table, select the key Category and value prod
8. Click Save to save the address object
9. Go to **Manage -> Profiles -> Network Threats**.
10. Click **Create Intrusion Profile**
11. Select **Data Loss Prevention**
12. Provide a name (e.g block_social_security)
13. In the DLP Filter List table, type US Social Security Number in the Patterns text column/field
14. Set 2 in the Count (sending more than 2 SSNs in the traffic would trigger the action)
15. Select Drop as the Action
16. Save the profile
17. Navigate to **Manage -> Profiles -> URL Filtering**.
18. Click on **Create** button.
19. Provide a name for the URL profile. (eg. allow-valtix-security-github)
20. Fill in the following information:

     Parameter | Value
     ----------|----------
     URLs/Categories | http.\*github.com/valtix-security.\*
     Methods | ALL
     Policy | Allow Log

21. Add another URL list by clicking on the **Add** button within the same profile.
22. Fill in the following information in the new URL list entry:

     Parameter | Value
     ----------|----------
     URLs/Categories | http.\*github.com/.\*
     Methods | ALL
     Policy | Deny Log

23. Click **Manage -> Security Policies -> Rules**
24. Click the "valtix-sample-egress-policy-ruleset" ruleset.
25. Click Create to create a new rule.  A new rule editor opens in the slide over panel on the right
26. Fill in the following information:

     Parameter| Value
     ---------|------
     Name | block_credit_card
     Type | Forward Proxy
     Service | valtix-sample-egress-forward-proxy
     Source | any
     Action | Deny Log
     Data Loss Prevention | block_social_security
     URL Filtering | allow-valtix-security-github

27. Click **Save**.
28. Move the newly created rule above the valtix-sample-egress-forwarding-allow-snat rule by dragging the rule to the top.
29. Click Save Changes.
<br><br>

## Verification

1. SSH to the EC2 instance created in the spoke1-vpc, spoke-z2-app
2. Execute `curl https://www.example.com -kv -d "613-63-6333 613-63-6333 613-63-6333" -o /dev/null`
3. Check that you get a 502 Bad Gateway error
4. Go to **Investigate -> Flow Analytics -> Network Threats**
5. You will note logs for the DLP dropped requests with a message: Sensitive Data was Transmitted Across the Network
6. Download a file from valtix-security repository on spoke1-vpc. `wget https://github.com/valtix-security/tutorials/raw/main/test.zip`. This connection should be allowed.
7. Download a file from a different github account. eg `wget https://github.com/michaelvaltix/tutorials/blob/main/test_file.txt`. This connection should be denied.
8. Navigate to **Investigate -> Flow Analytics -> URL Filtering**.
9. You should see both the allow session and the deny session for the 2 wget from github.
10. Notice that we did not specify any IP address in the policy, but the vm instance still matches the policy. This is because of the tag-based object that we used in the policy. This policy will be applied to any instance that has the tag **prod**. This allows for the policy to be dynamic. Future instances that is considered as prod environment will by this rule applied simply by adding tag value {Category: prod}  
