# Data protection in AWS Mainframe Modernization<a name="data-protection"></a>

The AWS [shared responsibility model](http://aws.amazon.com/compliance/shared-responsibility-model/) applies to data protection in AWS Mainframe Modernization\. As described in this model, AWS is responsible for protecting the global infrastructure that runs all of the AWS Cloud\. You are responsible for maintaining control over your content that is hosted on this infrastructure\. This content includes the security configuration and management tasks for the AWS services that you use\. For more information about data privacy, see the [Data Privacy FAQ](http://aws.amazon.com/compliance/data-privacy-faq)\. For information about data protection in Europe, see the [AWS Shared Responsibility Model and GDPR](http://aws.amazon.com/blogs/security/the-aws-shared-responsibility-model-and-gdpr/) blog post on the *AWS Security Blog*\.

For data protection purposes, we recommend that you protect AWS account credentials and set up individual user accounts with AWS Identity and Access Management \(IAM\)\. That way each user is given only the permissions necessary to fulfill their job duties\. We also recommend that you secure your data in the following ways:
+ Use multi\-factor authentication \(MFA\) with each account\.
+ Use SSL/TLS to communicate with AWS resources\. We recommend TLS 1\.2 or later\.
+ Set up API and user activity logging with AWS CloudTrail\.
+ Use AWS encryption solutions, along with all default security controls within AWS services\.
+ Use advanced managed security services such as Amazon Macie, which assists in discovering and securing personal data that is stored in Amazon S3\.
+ If you require FIPS 140\-2 validated cryptographic modules when accessing AWS through a command line interface or an API, use a FIPS endpoint\. For more information about the available FIPS endpoints, see [Federal Information Processing Standard \(FIPS\) 140\-2](http://aws.amazon.com/compliance/fips/)\.

We strongly recommend that you never put confidential or sensitive information, such as your customers' email addresses, into tags or free\-form fields such as a **Name** field\. This includes when you work with AWS Mainframe Modernization or other AWS services using the console, API, AWS CLI, or AWS SDKs\. Any data that you enter into tags or free\-form fields used for names may be used for billing or diagnostic logs\. If you provide a URL to an external server, we strongly recommend that you do not include credentials information in the URL to validate your request to that server\.



 AWS Mainframe Modernization collects two types of data from you: 
+ Application configuration\. This is a JSON file that you create to configure your application\. It contains your choices for the different options that AWS Mainframe Modernization offers\. The file also contains information for dependent AWS resources such as Amazon S3 paths where application artifacts are stored or the Amazon Resource Name \(ARN\) for Secrets Manager where your database credentials are stored\.
+  Application executable \(binary\)\. This is a binary that you compile and that you intend to deploy on AWS Mainframe Modernization\. 

## Encryption at rest<a name="encryption-rest"></a>

AWS Mainframe Modernization configures server side encryption \(SSE\) on all dependent resources that store data permanently; namely Amazon S3, DynamoDB, and Amazon EBS\. AWS Mainframe Modernization creates and manages encryption keys on your behalf in AWS KMS\. All of your data is encrypted with an AWS Mainframe Modernization\-managed key\. Currently, customer managed keys are not supported\. 

 Encryption at rest is configured by default\. You cannot disable it or change it\. Currently, you cannot change its configuration either\. 

## Encryption in transit<a name="encryption-transit"></a>

For interactive applications part of transactional workloads, the data exchanges between the terminal emulator and AWS Mainframe Modernization service endpoint for tn3270 protocol are not encryted in transit. Some additional tunneling mechanisms may be implemented by the customer when encryption in transit is required by the application.

AWS Mainframe Modernization uses HTTPS to encrypt the service APIs\. All other communication within AWS Mainframe Modernization is protected by the service VPC or security group, as well as HTTPS\. AWS Mainframe Modernization transfers application artifacts and configurations\. Application artifacts are copied from an Amazon S3 bucket that you own\. You can provide application configurations using a link to Amazon S3 or by uploading a file locally\.

 Encryption in transit is configured by default\.
