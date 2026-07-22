---
title: "Worklog Week 6"
date: 2026-05-25
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---

### Week 6 Objectives:

* Begin implementing the **CloudPay – Payment System on AWS** project.
* Build basic interfaces for the Customer Web and Admin Dashboard.
* Learn how to store static website files using Amazon S3.
* Learn how to distribute frontend content using Amazon CloudFront.
* Ensure that the S3 buckets cannot be accessed publicly and directly.

### Tasks to Be Completed This Week:

| Day | Task | Start Date | Completion Date | Reference |
| --- | ---- | ---------- | --------------- | --------- |
| Monday | - Define the frontend interfaces required for CloudPay.<br>- Create a basic HTML interface for the Customer Web.<br>- Create a basic HTML interface for the Admin Dashboard.<br>- Test the interfaces locally in a web browser. | 25/05/2026 | 25/05/2026 | HTML and CSS materials<br>FCAJ program materials |
| Tuesday | - Learn about Amazon S3 and static website file storage.<br>- Create two S3 buckets for the Customer Web and Admin Dashboard.<br>- Enable Block Public Access.<br>- Configure Bucket Owner Enforced and SSE-S3 encryption. | 26/05/2026 | 26/05/2026 | https://cloudjourney.awsstudygroup.com/<br>AWS documentation |
| Wednesday | - Upload the `index.html` files to both S3 buckets.<br>- Review the object list in each bucket.<br>- Attempt to access the files directly through S3 and confirm that an `AccessDenied` response is returned because the buckets are private. | 27/05/2026 | 27/05/2026 | AWS documentation<br>FCAJ program materials |
| Thursday | - Learn about Amazon CloudFront and Origin Access Control.<br>- Create a CloudFront distribution for the Customer Web.<br>- Create a CloudFront distribution for the Admin Dashboard.<br>- Configure the S3 buckets as origins and set `index.html` as the Default Root Object. | 28/05/2026 | 28/05/2026 | https://cloudjourney.awsstudygroup.com/<br>AWS documentation |
| Friday | - Update the bucket policies so that only CloudFront can access the S3 content.<br>- Test both websites through their CloudFront domain names.<br>- Create a CloudFront invalidation after updating website content.<br>- Record the issues and solutions encountered during deployment. | 29/05/2026 | 29/05/2026 | AWS documentation<br>FCAJ program materials |

### Week 6 Results:

* Completed the basic interfaces for:
  * Customer Web Application.
  * Admin Dashboard.
* Successfully created two S3 buckets for frontend file storage.
* Configured the following S3 security settings:
  * Block Public Access.
  * Bucket Owner Enforced.
  * SSE-S3 encryption.
* Successfully uploaded the frontend files to Amazon S3.
* Understood that private S3 buckets cannot be accessed directly from the Internet.
* Successfully created two Amazon CloudFront distributions.
* Configured Origin Access Control so that CloudFront could access the content stored in S3.
* Set `index.html` as the Default Root Object.
* Successfully accessed the Customer Web and Admin Dashboard through CloudFront.
* Learned how to create a CloudFront invalidation after updating website content.
* Understood the roles of Amazon S3 and CloudFront in securely deploying frontend applications on AWS.