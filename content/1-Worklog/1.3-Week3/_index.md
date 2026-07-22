---
title: "Worklog Week 3"
date: 2026-05-04
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---

### Week 3 Objectives:

* Learn about the Hybrid Cloud networking model on AWS.
* Understand the role and operation of Amazon Transit Gateway.
* Practice connecting multiple VPCs through a Transit Gateway.
* Learn how Amazon Route 53 Resolver supports DNS resolution between AWS and an on-premises environment.
* Practice provisioning infrastructure using AWS CloudFormation.
* Learn how to test and clean up resources after completing the lab.

### Tasks to Be Completed This Week:

| Day | Task | Start Date | Completion Date | Reference |
| --- | ---- | ---------- | --------------- | --------- |
| Monday | - Learn about the concept and functions of Amazon Transit Gateway.<br>- Study the architecture for connecting multiple VPCs.<br>- Create the VPCs required for the lab. | 04/05/2026 | 04/05/2026 | https://cloudjourney.awsstudygroup.com/<br>https://www.youtube.com/@AWSStudyGroup/videos |
| Tuesday | - Create an Amazon Transit Gateway.<br>- Configure the Amazon Side ASN and DNS Support settings.<br>- Create Transit Gateway Attachments to connect the VPCs.<br>- Learn about and create a Transit Gateway Route Table. | 05/05/2026 | 05/05/2026 | https://cloudjourney.awsstudygroup.com/<br>https://www.youtube.com/@AWSStudyGroup/videos |
| Wednesday | - Configure the Route Table for each VPC and subnet.<br>- Add routes from the VPCs to the Transit Gateway.<br>- Test connectivity between EC2 instances by using the ping command.<br>- Review and troubleshoot basic network configuration issues. | 06/05/2026 | 06/05/2026 | https://cloudjourney.awsstudygroup.com/<br>https://www.youtube.com/@AWSStudyGroup/videos |
| Thursday | - Learn how to provision infrastructure using an AWS CloudFormation template.<br>- Configure Security Groups for DNS, RDP, and SSH.<br>- Create a Route 53 Resolver Outbound Endpoint.<br>- Create and configure Resolver Rules. | 07/05/2026 | 07/05/2026 | https://cloudjourney.awsstudygroup.com/<br>https://www.youtube.com/@AWSStudyGroup/videos |
| Friday | - Create a Route 53 Resolver Inbound Endpoint.<br>- Test the routing and Hybrid DNS configuration.<br>- Verify DNS resolution between the environments.<br>- Clean up the resources after completing the lab:<br>&emsp; + Delete Resolver Rules<br>&emsp; + Delete Resolver Endpoints<br>&emsp; + Delete Transit Gateway Attachments<br>&emsp; + Delete the Transit Gateway and related Route Tables | 08/05/2026 | 08/05/2026 | https://cloudjourney.awsstudygroup.com/<br>https://www.youtube.com/@AWSStudyGroup/videos |

### Week 3 Results:

* Understood the concept and role of Amazon Transit Gateway in connecting multiple VPCs.
* Created and configured the VPCs required for the lab.
* Deployed an Amazon Transit Gateway to connect multiple VPCs within the same network architecture.
* Created and configured Transit Gateway Attachments.
* Configured Route Tables to route traffic between VPCs through the Transit Gateway.
* Tested connectivity between EC2 instances located in different VPCs.
* Learned how to provision infrastructure using an AWS CloudFormation template.
* Understood the Hybrid DNS model on AWS.
* Successfully created and configured:
  * Route 53 Resolver Outbound Endpoint
  * Route 53 Resolver Inbound Endpoint
  * Resolver Rules
* Tested DNS resolution between AWS and the simulated on-premises environment.
* Learned how to review Security Groups, Route Tables, and DNS configurations when troubleshooting connectivity issues.
* Cleaned up the resources after completing the lab to reduce the risk of unexpected charges.