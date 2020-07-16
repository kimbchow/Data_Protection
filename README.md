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

Key Terms and Concepts:
* Root CA – the root CA is the highest level of the hierarchy and serves as the trust anchor. In order for an end entity certificate to be trusted, the root CA it chains up to must be embedded in the operating system, browser, device, or whatever is validating the certificate. Root CAs are heavily secured. When you create a Root CA, the Root CA private key is used to sign the root CA Certificate Signing Request (CSR). The Root CA private key for AWS Certificate Manager in a hierarchy protects the root CA private keys in FIPS 140-2 Level 3 validated Hardware. 

* Subordinate CAs – these live between the root and end entity certificates and their main purpose is to define and authorize the types of certificates that can be requested from the root CA.    

* End entity certificates – these are the certificates installed on servers, machines, cryptographic hardware and devices (e.g. SSL/TLS issued to servers, code signing, client certificates issued to individuals for email encryption, digital signing, authentication)

## Create a Root CA 

1. Assume an IAM Role called **CaAdminRole** 
Assume the role named CaAdminRole by using switch role on the AWS console in the AWS account that you are currently logged into. The role name is case sensitive. This role has permissions that a Certificate Authority administrator will need for CA administration. As a CA administrator you will be responsible for creating a root and subordinate certificate authority hierarchy

Step 1: 
Navigate to AWS Certificate Manager Service in the AWS Console. Click on "Get Started" under the Private Certificate Authority. 
![Screen Shot 2020-07-16 at 11 50 04 AM](https://user-images.githubusercontent.com/50940575/87624496-87599300-c75a-11ea-8975-a4badb2b0daf.png)

Step 2: Configure the Root CA parameters
![Screen Shot 2020-07-16 at 11 50 48 AM](https://user-images.githubusercontent.com/50940575/87624539-9dffea00-c75a-11ea-8a51-0632d5d492fd.png)

Step 3: Configure the CA Key Algorithm - expand to see the different types of algorithms you can choose from. 
![Screen Shot 2020-07-16 at 11 51 49 AM](https://user-images.githubusercontent.com/50940575/87624607-c1c33000-c75a-11ea-957d-ec4f240b4b8c.png)

Step 4: Enable the Certificate Revocation List (CRL) Distribution. A certificate revocation list (or CRL) is a list of digital certificates that have been revoked by the issuing certificate authority (CA) before their scheduled expiration date and should no longer be trusted. 
![Screen Shot 2020-07-16 at 11 53 20 AM](https://user-images.githubusercontent.com/50940575/87624724-fafba000-c75a-11ea-869e-21c6ae322d9a.png)

Step 5: Set tags - Remember this feature from last week's ABAC workshop. 
![Screen Shot 2020-07-16 at 11 54 13 AM](https://user-images.githubusercontent.com/50940575/87624762-1797d800-c75b-11ea-895a-e7c3c5c02a4a.png)

Step 6: Configure CA permissions. We do not want to issue end entity certs from the root CA. Click next and confirm and create the root CA.
![Screen Shot 2020-07-16 at 11 57 42 AM](https://user-images.githubusercontent.com/50940575/87625179-1d41ed80-c75c-11ea-9dff-425ca28d1457.png)

Step 7: Click "Get Started" to activate the CA
![Screen Shot 2020-07-16 at 12 03 05 PM](https://user-images.githubusercontent.com/50940575/87625276-537f6d00-c75c-11ea-8131-22fc4b86399e.png)

Step 8: Set the validity period and signature algorithm for the Root CA cert. Click next and confirm and install to create the root CA cert. 

![image](https://user-images.githubusercontent.com/50940575/87625326-73169580-c75c-11ea-8de9-0cb9e0aa261b.png)

Step 9: Success! You should see that the root CA is now active. 
![Screen Shot 2020-07-16 at 12 05 08 PM](https://user-images.githubusercontent.com/50940575/87625422-b113b980-c75c-11ea-9507-fa1a48c11fc8.png)

## Create a Subordinate Issuing CA

* Navigate to ACM Service in the AWS Console
* Under Private CA's click on the Create CA button

Step 1: Create a subordinate issuing CA

![Screen Shot 2020-07-16 at 12 43 35 PM](https://user-images.githubusercontent.com/50940575/87627752-fdadc380-c761-11ea-9a41-5cce0a085004.png)

Step 2: Configure the CA with these parameters
![Screen Shot 2020-07-16 at 12 44 30 PM](https://user-images.githubusercontent.com/50940575/87627796-1ae29200-c762-11ea-9bed-67c308313a01.png)

Step 3: Choose the CA key Algorithm 
![Screen Shot 2020-07-16 at 12 45 12 PM](https://user-images.githubusercontent.com/50940575/87627829-364d9d00-c762-11ea-9d65-5c4a08ef0bd3.png)

Step 4: Enable the CRL distribution
![Screen Shot 2020-07-16 at 12 46 07 PM](https://user-images.githubusercontent.com/50940575/87627882-567d5c00-c762-11ea-9284-7557c1079a60.png)

Step 5: Add tags
![Screen Shot 2020-07-16 at 12 46 49 PM](https://user-images.githubusercontent.com/50940575/87627928-6c8b1c80-c762-11ea-8efa-a42378132ce1.png)

Step 6: Authorise this subordinate CA to manage renewals, click next and confirm and create the CA. 
![Screen Shot 2020-07-16 at 12 47 15 PM](https://user-images.githubusercontent.com/50940575/87627975-86c4fa80-c762-11ea-8f4e-e7ec8089fa17.png)

Step 7: Click "Get Started" to activate the subordinate CA cert.
![Screen Shot 2020-07-16 at 12 48 41 PM](https://user-images.githubusercontent.com/50940575/87628066-ae1bc780-c762-11ea-816b-e61a835cde9f.png)

Step 8: Install the subordinate CA cert, and sign it with the root CA that was created earlier. Please note that for signing the subordinate CA CSR, there are two options : You can either use an ACM Private CA that's created with ACM Private CA OR you can use an intermediate or root private CA that already exists within your organization. 
![Screen Shot 2020-07-16 at 12 49 39 PM](https://user-images.githubusercontent.com/50940575/87628115-d5729480-c762-11ea-8a94-ad0ac9e18f43.png)

Step 9: Select a parent Private CA - root CA. Please note that you set the path length as Zero. Path length (pathlen) is a CA certificate basic constraint that defines the maximum CA depth of the CA hierarchy existing under a CA. For example, a CA with a path length constraint of zero cannot have any subordinate CAs. A CA with a path length constraint of one may have up to one level of subordinate CAs underneath it. Setting the path length to zero has the added security benefit of:
* Preventing creation of any more subordinate CAs that are not authorised

![Screen Shot 2020-07-16 at 12 52 26 PM](https://user-images.githubusercontent.com/50940575/87628319-3ac68580-c763-11ea-80ef-3e5d70ed4d9f.png)

Step 10: Review and Generate the subordinate CA certificate
![Screen Shot 2020-07-16 at 12 54 47 PM](https://user-images.githubusercontent.com/50940575/87628460-97c23b80-c763-11ea-9dd2-24d63c7a4f4a.png)

Step 11: Success! You should see that the subordinate CA with common name acmsubordinatecag1 is now set to Active. 

## Issuing the private certificate created on the Application Load Balancer 

Step 1: Assume an IAM Role called **AppDevRole** following the steps above to assume a new role. This role has permissions that a Application developer will need for building an web aplication which is fronted by an application load balancer and behind the load balancer is a lambda origin that provides the HTML code for a website. The application developer will also have permissions to issue a certificate under a certificate authority that they select.

Step 2: Navigate to the EC2 Console, and scroll down until you see "Load Balancers" on the left side menu. You should see a Load Balancer has already been deployed. Copy out the DNS name of the ALB that is deployed. Save this somewhere as you will use this in later steps. 
![Screen Shot 2020-07-16 at 12 59 15 PM](https://user-images.githubusercontent.com/50940575/87628739-30f15200-c764-11ea-85a9-8e4ef4c57b18.png)

Step 3: Issue a Private Certificate from ACM by navigating back to ACM. 
![Screen Shot 2020-07-16 at 1 00 12 PM](https://user-images.githubusercontent.com/50940575/87628799-4f574d80-c764-11ea-96ec-c8c964e5f9f9.png)

Step 4: Click on Request a private certificate
![Screen Shot 2020-07-16 at 1 01 07 PM](https://user-images.githubusercontent.com/50940575/87628861-70b83980-c764-11ea-8b34-398235f8a36e.png)

Step 5: Select the subordinate CA you created earlier. The private certificate issued will be signed by the subordinate issuing CA. 
![Screen Shot 2020-07-16 at 1 02 36 PM](https://user-images.githubusercontent.com/50940575/87628972-a65d2280-c764-11ea-8ac3-f6558af7fbf1.png)

Step 6: Secure by applying the cert to the ALB DNS name that you copied in step 2. Click Next. Skip the next step, do not add any tags. 
![Screen Shot 2020-07-16 at 1 04 42 PM](https://user-images.githubusercontent.com/50940575/87629114-f1773580-c764-11ea-81cb-a083a13606f6.png)

Step 7: Verify that the domain name is accurate and click confirm and request. 
![Screen Shot 2020-07-16 at 1 05 48 PM](https://user-images.githubusercontent.com/50940575/87629187-1cfa2000-c765-11ea-9410-240b86677b84.png)

Step 8: Success!! Your certificate should be created successfully. Please note that when you issue a private certificate from the console, the validity period is set to a default of 13 months. ACM automatically manages the renewals for this certificate before it expires. If you want flexibility in setting a different validity period for the private certificates you would like to generate, you can use the issue certificate ACM Private CA API.  However these certificates will not be automatically renewed by ACM. You will have to manually renew them. For more information on the issue_certificate API Call for acmpca please see the documenation link below :

https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/acm-pca.html#ACMPCA.Client.issue_certificate


## Attaching a HTTPS listener and private certificate to the ALB 

Step 1: Navigate back to EC2. Scroll down on the left hand menu until you see "Target Groups". Click on Target Groups and Create Target Group. 
![Screen Shot 2020-07-16 at 1 09 09 PM](https://user-images.githubusercontent.com/50940575/87629402-9134c380-c765-11ea-8c8f-f787bab2e323.png)

Step 2: Specify the target group details as the Lambda Function. This routes load balancer requests to this Lambda function. Click Next. 
![Screen Shot 2020-07-16 at 1 10 32 PM](https://user-images.githubusercontent.com/50940575/87629510-c17c6200-c765-11ea-8fde-825eb357b4c3.png)

Step 3: Choose the builders-lambda-origin-one function
![Screen Shot 2020-07-16 at 1 11 34 PM](https://user-images.githubusercontent.com/50940575/87629593-e53fa800-c765-11ea-89c9-9b84f160c9e6.png)

Step 4: Navigate back to the EC2 console, and scroll down to 'Load Balancers' on the left hand side menu. Navigate to "Listeners" to Add a Listener on your ALB. 
![Screen Shot 2020-07-16 at 1 12 41 PM](https://user-images.githubusercontent.com/50940575/87629677-0e603880-c766-11ea-9899-8d9721f44498.png)

Step 5: Configure your HTTPS listener with the following parameters. Click Save on the top right of the screen. 
![Screen Shot 2020-07-16 at 1 13 30 PM](https://user-images.githubusercontent.com/50940575/87629767-32237e80-c766-11ea-8ffd-7ea808381cb0.png)

Step 6: Success! You have now configured your HTTPS listener and private certificate to the ALB. 

Please note - The ALB created is internet facing, however a private cert was applied to the HTTPS listener of the ALB so that the identity of the ALB can be visually validated by your browser in later steps. This was done for education purposes. Private Certificates are usually meant for applications that are accessible ONLY over a private network. If you are building a public internet facing application, you should use public ACM certificates. 

## OPTIONAL - Validate the identity of the ALB with the browser that your are using. Please open link in a new browser tab
* For firefox : https://view.highspot.com/viewer/5d5c1fe23f65f635ae005a47
* For google chrome : https://view.highspot.com/viewer/5d5c42da66bbaa2fc928a575
* For Microsoft Edge : https://view.highspot.com/viewer/5d5c2e5cf7794d4833e8207a

12. Cleanup
* Don't worry about cleanup, we will take care of it. Hopefully you learnt something useful in this workshop that you can take back your organization. Thank you for coming.
