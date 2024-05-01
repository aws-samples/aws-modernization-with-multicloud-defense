---
title: "Introduction"
chapter: true
weight: 1
---

Cisco Multicloud Defense is a highly scalable, on-demand **“as-a-Service”** solution that provides agile, scalable, and flexible security to your multicloud infrastructure. It unifies security controls across cloud environments, protects workloads from every direction, and drives operational efficiency by leveraging secure cloud networking.
Cisco Multicloud Defense uses a common principle in public clouds and software-defined networking (SDN) which decouples the control and data plane, translating to the **Multicloud Defense Controller** and the **Multicloud Defense Gateways**.

![Cisco Multicloud Defense](/static/14-content/Multicloud_Defense_overview.png)

- **Multicloud Defense Controller (Software-as-a-Service):** The Multicloud Defense Controller is a highly reliable and scalable centralized controller (control plane) that automates, orchestrates, and secures multicloud infrastructure. It runs as a Software-as-a-Service (SaaS) and is fully managed by Cisco. 
<br> </br>
- **Multicloud Defense Gateway (Platform-as-a-Service):** The Multicloud Defense Gateway is an auto-scaling fleet of security software with a patented flexible, single-pass pipelined architecture. The Multicloud Defense Controller deploys these gateways as Platform-as-a-Service (PaaS) into the customer’s public cloud account(s), providing advanced, inline security protections to defend against external attacks, block egress data exfiltration and prevent the lateral movement of attacks.


---
# Multicloud Defense Advance Security

The Cisco Multicloud Defense Gateway provides the following security capabilities: 

![Cisco Multicloud Defense Gateway](/static/14-content/Multicloud_Defense_capabilities.png)


---
# Deployment Model

Cisco Multicloud Defense supports both centralized and distributed models. This lab will focus on the centralized model

![Cisco Multicloud Defense Gateway](/static/14-content/Multicloud_Defense_model.png)

- **Centralized Security Model:** In the centralized security model, the Multicloud Defense Gateway(s) are deployed in a dedicated security VPC/VNet in the customer’s account. 
- **Distributed Security Model:** In the distributed security model, the Multicloud Defense Gateway(s) are deployed in the application VPC/VNet. 


Multicloud Defense provides network visibility and control of your public cloud deployments to protect them for a variety of scenarios: ingress, egress, and east-west. Managing cloud infrastructure is a nightmare as cloud deployments happen at the touch of a button. Security maintenance of patching and upgrading software may disrupt the network service, resulting in countless hours of troubleshooting. Valtix sees the pain and hence provides users with:

- **Visibility:** A detailed view of the inventory and traffic in the network
- **Speed:** deploy security in minutes
- **Dyanmic Policies:** policies that are still effective even as resource changes.



















