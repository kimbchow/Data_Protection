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
* Click on File > **Save Page As** > leave the default template name > Save within the folder **workshop_templates** created in the previous step. 
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

**Deploying the Cloudformation Templates**
* Please note that you will not be able to perform subsequent steps if the CloudFormation templates have not been deployed. Deploying these templates should take about 3-5 minutes each. 
* Build the infrastructure needed for these labs by deploying all 3 CloudFormation Templates. 
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
* Repeat the same steps for all 3 CloudFormation templates within your **workshop_templates** folder. 
* You should have 3 completed stacks at the end of this prerequisites section. 

<img width="644" alt="Screen Shot 2020-07-13 at 12 13 51 PM" src="https://user-images.githubusercontent.com/50940575/87270639-503d7480-c503-11ea-8e0c-1b582ca0a146.png">


## Part 1 : Using KMS for encrypting data on AWS services
As a first step, please download https://raw.githubusercontent.com/aws-samples/data-protection/master/usecase-8/files/to_upload.txt to your laptop by right clicking link and saving the link as the filename **to_upload.txt** to your **workshop_templates** folder. You will be uploading this file into an S3 bucket and encrypting it.

Part 1 of this workshops will cover two scenarios :

**AWS Managed Keys** :
Server side encryption on S3 using AWS Managed Keys. You will be uploading the .txt file downloaded into S3, and learning how to encrypt the file using AWS Managed Keys. Server-side encryption protects data at rest. Amazon S3 encrypts each object with a unique key. As an additional safeguard, it encrypts the key itself with a master key that it rotates regularly. Amazon S3 server-side encryption uses one of the strongest block ciphers available to encrypt your data, 256-bit Advanced Encryption Standard (AES-256).

Step 1: In the AWS Management Console, Click on Switch Role
<img width="1288" alt="Screen Shot 2020-07-16 at 7 37 59 AM" src="https://user-images.githubusercontent.com/50940575/87618951-9f76e580-c74d-11ea-91fe-3855d06d968f.png">

Step 2: Click on Switch Role Button
![Screen Shot 2020-07-16 at 10 18 37 AM](https://user-images.githubusercontent.com/50940575/87619000-bf0e0e00-c74d-11ea-85f6-554a10128078.png)

Step 3: Assume the **application-1-role** 
![Screen Shot 2020-07-16 at 10 19 35 AM](https://user-images.githubusercontent.com/50940575/87619067-e238bd80-c74d-11ea-8a96-aca0af060956.png)

Step 4: Verify that you have switched roles 
![Screen Shot 2020-07-16 at 10 20 22 AM](https://user-images.githubusercontent.com/50940575/87619119-fed4f580-c74d-11ea-824c-616fbb60c56e.png)

Step 5: Navigate to the S3 service
![Screen Shot 2020-07-16 at 10 22 00 AM](https://user-images.githubusercontent.com/50940575/87619197-3a6fbf80-c74e-11ea-898a-2bfdfc33f015.png)

Step 6: Open the S3 bucket with the name **bucket-kms-workshop** and upload the file named **to_upload.txt** to this bucket
![Screen Shot 2020-07-16 at 10 51 36 AM](https://user-images.githubusercontent.com/50940575/87620954-5d03d780-c752-11ea-90ac-243147361a2b.png)

Step 7: Upload the file to this bucket
![Screen Shot 2020-07-16 at 10 53 01 AM](https://user-images.githubusercontent.com/50940575/87621057-8de40c80-c752-11ea-81c0-cf910565adc3.png)

Step 8: Do not change any configurations of this S3 object. Just click next. 
![Screen Shot 2020-07-16 at 10 54 10 AM](https://user-images.githubusercontent.com/50940575/87621149-b66c0680-c752-11ea-877a-60c7124a9f95.png)

Step 9: Scroll down on this page until you see "Encryption" - You will need to set encryption configurations
![Screen Shot 2020-07-16 at 10 55 08 AM](https://user-images.githubusercontent.com/50940575/87621210-d996b600-c752-11ea-8352-9a9a44845b42.png)

Step 10: Under Review, Click Upload
![Screen Shot 2020-07-16 at 10 56 36 AM](https://user-images.githubusercontent.com/50940575/87621297-0cd94500-c753-11ea-8651-06b6c1d1506f.png)

Step 11: Check the object properties to see that you have used SSE-S3 
![Screen Shot 2020-07-16 at 10 57 46 AM](https://user-images.githubusercontent.com/50940575/87621372-398d5c80-c753-11ea-8825-d8480769b05e.png)

Step 12: Understand how AWS S3 Managed Keys makes it easy to perform encrypt/decrypt operations by looking at the IAM policies
![Screen Shot 2020-07-16 at 11 00 35 AM](https://user-images.githubusercontent.com/50940575/87621546-9ab53000-c753-11ea-9552-c8ebdbb61f71.png)

Step 13: Search for the application-1-role and look at its permissions
![Screen Shot 2020-07-16 at 11 01 26 AM](https://user-images.githubusercontent.com/50940575/87621593-b8829500-c753-11ea-9365-938977f224a8.png)

Step 14: Expand the **application-1-role-policy** and note that there are no explicit encrypt to decrypt permissions. However, you are still enable to encrypt and decrypt objects within S3 using AWS Managed Keys for S3. 
![Screen Shot 2020-07-16 at 11 02 44 AM](https://user-images.githubusercontent.com/50940575/87621679-e8319d00-c753-11ea-9f15-2cf927f621da.png) 


**Customer Managed Keys(CMK)** :
Server side encryption on S3 using Customer Managed Keys with separation of duties between key administrators and key users. In this part, you will be encrypting an S3 object using a Customer Managed Key created using AWS Key Management Service (KMS). 

Step 1: Assume the **key-admin-role**
![Screen Shot 2020-07-16 at 11 09 43 AM](https://user-images.githubusercontent.com/50940575/87622073-e2888700-c754-11ea-8831-972f7ab9cb7e.png)

Step 2: Navigate to the Key Management Service
![Screen Shot 2020-07-16 at 11 10 19 AM](https://user-images.githubusercontent.com/50940575/87622121-fd5afb80-c754-11ea-951a-f5758df870f3.png)

Step 3: Find the **bucketencryptionalias** Customer Managed Keys
![Screen Shot 2020-07-16 at 11 11 29 AM](https://user-images.githubusercontent.com/50940575/87622181-211e4180-c755-11ea-8758-128fdf8f9e0a.png)

Step 4: Look at the Key Policy to understand what permissions have been delegated to IAM 
![Screen Shot 2020-07-16 at 11 12 23 AM](https://user-images.githubusercontent.com/50940575/87622242-43b05a80-c755-11ea-82ae-f88e3115acba.png)

Step 5: Navigate to the IAM console, under Roles, search for the **key-admin-role**. Note that the key-admin-role has explicit permissions to create, revoke, as well as delete CMKs in AWS KMS. 
![Screen Shot 2020-07-16 at 11 14 42 AM](https://user-images.githubusercontent.com/50940575/87622371-94c04e80-c755-11ea-9909-8607eeb6f4f9.png)

Step 6: Assume the **application-2-role**
![Screen Shot 2020-07-16 at 11 16 51 AM](https://user-images.githubusercontent.com/50940575/87622523-e1a42500-c755-11ea-9b14-d266f9c3a277.png)

Step 7: Navigate to the S3 service and to the same **bucket-kms-workshop** bucket. Delete the object that was uploaded previously. 
![Screen Shot 2020-07-16 at 11 17 57 AM](https://user-images.githubusercontent.com/50940575/87622598-06989800-c756-11ea-9b44-6894f89f51d8.png)

Step 8: Re-Upload the object. You will now be using the **bucketencyprtionalias** CMK to encrypt this object instead. 
![Screen Shot 2020-07-16 at 11 19 19 AM](https://user-images.githubusercontent.com/50940575/87622684-38a9fa00-c756-11ea-92f8-9e031a336693.png)

Step 9: Do not change any configurations on the next page
![Screen Shot 2020-07-16 at 11 20 01 AM](https://user-images.githubusercontent.com/50940575/87622730-511a1480-c756-11ea-9195-aa77605fec40.png)

Step 10: Scroll down on the next page until you see Encryption options. Under encryption options, choose the bucketencryptionalias CMK as the Key. Upload the Object.
![Screen Shot 2020-07-16 at 11 20 58 AM](https://user-images.githubusercontent.com/50940575/87622802-7444c400-c756-11ea-8ea3-73a98b2783ef.png)

Step 11: Navigate back to the IAM console, and see permissions attached to the **application-2-role-policy**. Note that this role only has permissions to perform cryptographic operations (e.g. encrypt / decrypt), and not permissions to create or delete customer managed keys. This encryption permission is the policy that allows this role to upload the object and encrypt the object using the KMS key. 
![Screen Shot 2020-07-16 at 11 22 19 AM](https://user-images.githubusercontent.com/50940575/87622873-a2c29f00-c756-11ea-94a4-f56a7b850991.png)

Step 12: Test that this **application-2-role** does not have admin permissions to delete the CMK 
![Screen Shot 2020-07-16 at 11 24 40 AM](https://user-images.githubusercontent.com/50940575/87623020-faf9a100-c756-11ea-99f7-6b57e98c09cb.png)

Step 13: Attempt to Schedule Key Deletion for this CMK
![Screen Shot 2020-07-16 at 11 25 32 AM](https://user-images.githubusercontent.com/50940575/87623060-1b296000-c757-11ea-9f1c-4d68f25e034a.png)
![Screen Shot 2020-07-16 at 11 26 19 AM](https://user-images.githubusercontent.com/50940575/87623091-309e8a00-c757-11ea-9ff1-de8759156b98.png)

Step 14: Validate that this role cannot delete the key as the role is not a key admin. Always remember that there should be a separation of duties between the key administrator and key user. 
![image](https://user-images.githubusercontent.com/50940575/87623141-4b70fe80-c757-11ea-8094-4165205bc126.png)

**Optional: Assume a role as the key admin, and try and delete the key. You can also choose to cancel the deletion of the key once you are done. 

## Part 2 : Create a complete CA hierarchy using ACM Private CA and deploying private certificates

1. Assume an IAM Role called **CaAdminRole** 
Assume the role named CaAdminRole by using switch role on the AWS console in the AWS account that you are currently logged into. The role name is case sensitive

This role has permissions that a Certificate Authority administrator will need for CA administration. As a CA administrator you will be responsible for creating a root and subordinate certificate authority hierarchy

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
