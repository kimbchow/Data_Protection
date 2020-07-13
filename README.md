# Data_Protection
KMS and ACM Private Certificate Authority (ACM Private CA) Workshop

This workshop has two parts :

1.First part demonstrates how you can use KMS with S3 for server side encryption while storing objects on S3. Different encryption options and separation of duties between a key administrator and key user is also explored. 

2.The second part is about how ACM Private Certificate Authority (PCA) service can be used to create a complete CA hierarchy, generate a private certificate, and apply the private certificate on an Application Load Balancer while following security best practices.

## Setting up on Event Engine with an AWS provided account: 
* You will be using an AWS provided account for this workshop :
* Log in with your hash provided on the piece of paper which says Event Engine Hash or by clicking on the URL provided. 
* Click on the AWS Console button
It should bring up a pop-up screen. On the pop-up, under Login Link click on Open Console
* You should be logged into the AWS provided account
* Please verify that the region selected is eu-west-1 (Ireland). 

## Prerequisites:
This step will help you copy 3 AWS CloudFormation Templates that are required for both parts of the workshop. 

* Create a local folder entitled **workshop_templates** on your laptop. You will need to save the 3 templates below into this folder for easy navigation. 

**Setting up the CloudFormation template-base for the workshop**
* Click on https://raw.githubusercontent.com/aws-samples/data-protection/master/usecase-8/cf-templates/template-base.yaml 
* On Mac:
* Click on Chrome > **Save Page As** > leave the default template name > Save within the folder **workshop_templates** created in the previous step. 
<img width="424" alt="Screen Shot 2020-07-13 at 11 48 10 AM" src="https://user-images.githubusercontent.com/50940575/87269157-ed49de80-c4fe-11ea-8afe-5b12d7da3da6.png">


* On Windows: 
* Click on https://raw.githubusercontent.com/aws-samples/data-protection/master/usecase-8/cf-templates/template-base.yaml 
* Right Click > **Save As** > leave the default template name > Save within the folder **workshop_templates** created in the previous step. 
<img width="711" alt="Screen Shot 2020-07-13 at 11 53 38 AM" src="https://user-images.githubusercontent.com/50940575/87269418-adcfc200-c4ff-11ea-9d1e-93265f9fc16a.png">

**Setting up the CloudFormation template-ca-admin for the workshop**
* Click on https://raw.githubusercontent.com/aws-samples/data-protection/master/usecase-8/cf-templates/template-ca-admin.yaml
* On Mac:
* Click on Chrome > **Save Page As** > leave the default template name > Save within the folder **workshop_templates** created in the previous step.

* On Windows: 
* Click on https://raw.githubusercontent.com/aws-samples/data-protection/master/usecase-8/cf-templates/template-ca-admin.yaml
* Right Click > **Save As** > leave the default template name > Save within the folder **workshop_templates** created in the previous step. 

**Setting up the CloudFormation template-app-dev for the workshop**
* Click on https://raw.githubusercontent.com/aws-samples/data-protection/master/usecase-8/cf-templates/template-app-dev.yaml
* On Mac:
* Click on Chrome > **Save Page As** > leave the default template name > Save within the folder **workshop_templates** created in the previous step.

* On Windows: 
* Click on https://raw.githubusercontent.com/aws-samples/data-protection/master/usecase-8/cf-templates/template-app-dev.yaml
* Right Click > **Save As** > leave the default template name > Save within the folder **workshop_templates** created in the previous step. 

**You should now have 3 AWS CloudFormation Templates within your workshop_templates folder 

## Part 1 : Using KMS for encrypting data on AWS services
As a first step, please download https://raw.githubusercontent.com/aws-samples/data-protection/master/usecase-8/files/to_upload.txt to your laptop by right clicking the below link and save link as the filename to_upload.txt to any folder on your laptop in the **workshop_templates** folder. 

This workshops will cover two scenarios :

**AWS Managed Keys** :
Server side encryption on S3 using AWS Managed Keys

For instructions, right click and open this link in a new browser tab : https://view.highspot.com/viewer/5d9e69b634d6be6992300ade

**Customer Managed Keys(CMK)** :
Server side encryption on S3 using Customer Managed Keys with separation of duties between key administrators and key users.

For instructions, right click and open this link in a new browser tab : https://view.highspot.com/viewer/5d9e6e10f7794d2efc187cbe

## Part 2 : Create a complete CA hierarchy using ACM Private CA and deploying private certificates

1. Assume an IAM Role called **CaAdminRole** 
Assume the role named CaAdminRole by using switch role on the AWS console in the AWS account that you are currently logged into. The role name is case sensitive

This role has permissions that a Certificate Authority administrator will need for CA administration. As a CA administrator you will be responsible for creating a root and subordinate certificate authority hierarchy


2. Build the infrastructure needed for creating a CA hierarchy by deploying the cloudformation template below for **templates-ca-admin**
* Navigate to the CloudFormation console
* Click Create Stack. Click Next. 

<img width="1063" alt="Screen Shot 2020-07-13 at 12 12 19 PM" src="https://user-images.githubusercontent.com/50940575/87270405-934b1800-c502-11ea-87cf-ec8fbb2ce131.png">

* Upload the **template-base.yaml.txt** file to cloudformation. Click Next. 

<img width="1003" alt="Screen Shot 2020-07-13 at 12 12 45 PM" src="https://user-images.githubusercontent.com/50940575/87270456-bece0280-c502-11ea-8af0-76dbb2f2fba7.png">

* Name the stack **template-base**. Click Next. 

<img width="1030" alt="Screen Shot 2020-07-13 at 12 13 08 PM" src="https://user-images.githubusercontent.com/50940575/87270514-e91fc000-c502-11ea-990e-daf9d70a7b82.png">

* On the next page, scroll all the way down and tick the checkbox next to "I acknowledge that AWS CloudFormation might create IAM resources with custom IAM names".

<img width="1029" alt="Screen Shot 2020-07-13 at 12 13 36 PM" src="https://user-images.githubusercontent.com/50940575/87270589-23895d00-c503-11ea-8e90-b70b97632f9d.png">

* Click Create Stack. 
* Repeat the same steps for 3 of the 3 CloudFormation templates within your folder. 
* You should have 3 completed stacks at the end of this section. 

<img width="644" alt="Screen Shot 2020-07-13 at 12 13 51 PM" src="https://user-images.githubusercontent.com/50940575/87270639-503d7480-c503-11ea-8e0c-1b582ca0a146.png">


3. Create a Root CA.
* Navigate to ACM Service in the AWS Console
* Click Get Started under Private Certificate Authority
* Open this link in a new browser tab for the rest of the steps : https://view.highspot.com/viewer/5d5b129b6a3b116f4230f242 

4. Create a Subordinate Issuing CA.
* Navigate to ACM Service in the AWS Console
* Under Private CA's click on the Create CA button
* Open this link in a new browser tab for the rest of the steps : https://view.highspot.com/viewer/5d9e91c1a2e3a9148b6d7deb 

6. Assume an IAM Role called **AppDevRole** 
Assume the role named AppDevRole by using switch role on the AWS console in the AWS account that you are currently logged into.

This role has permissions that a Application developer will need for building an web aplication which is fronted by an application load balancer and behind the load balancer is a lambda origin that provides the HTML code for a website. The application developer will also have permissions to issue a certificate under a certificate authority that they select.

7. Next step is to issue a private certificate to put on the application load balancer.
* Open this link in a new browser tab for steps : https://view.highspot.com/viewer/5d5b133d6a3b116f29313a10

9. Attach a HTTPS listener and private certificate to the ALB .
* Open this link in a new browser tab for steps : https://view.highspot.com/viewer/5d669c21628ba22ca196b49e

10. Validate the identity of the ALB with the browser that your are using. Please open link in a new browser tab
* For firefox : https://view.highspot.com/viewer/5d5c1fe23f65f635ae005a47
* For google chrome : https://view.highspot.com/viewer/5d5c42da66bbaa2fc928a575
* For Microsoft Edge : https://view.highspot.com/viewer/5d5c2e5cf7794d4833e8207a

12. Cleanup
* Don't worry about cleanup, we will take care of it. Hopefully you learnt something useful in this workshop that you can take back your organization. Thank you for coming.
