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



 AWS Mainframe Modernization collects several types of data from you: 
+ Application configuration\. This is a JSON file that you create to configure your application\. It contains your choices for the different options that AWS Mainframe Modernization offers\. The file also contains information for dependent AWS resources such as Amazon Simple Storage Service paths where application artifacts are stored or the Amazon Resource Name \(ARN\) for AWS Secrets Manager where your database credentials are stored\.
+  Application executable \(binary\)\. This is a binary that you compile and that you intend to deploy on AWS Mainframe Modernization\. 
+ Application JCL or scripts\. This source code manages batch jobs or other processing on behalf of your application\.
+ User application data\. When you import data sets, AWS Mainframe Modernization stores them in the relational database so your application can access them\.
+ Application source code\. Through Amazon AppStream 2\.0, AWS Mainframe Modernization provides a development environment for you to write and compile code\. 

AWS Mainframe Modernization stores this data natively in AWS\. The data we collect from you is stored in an AWS Mainframe Modernization\-managed Amazon S3 bucket\. When you deploy an application, AWS Mainframe Modernization downloads the data onto an Amazon Elastic Block Store\-backed Amazon Elastic Compute Cloud instance\. When cleanup is triggered, the data is removed from the Amazon EBS volume and from Amazon S3\. The Amazon EBS volumes are single\-tenanted, meaning that one instance is used for one customer\. Instances are never shared\. When you delete a runtime environment, the Amazon EBS volume is also deleted\. When you delete an application, the artifacts and configuration are deleted from Amazon S3\. 

Application logs are stored in Amazon CloudWatch\. Customer application log messages are exported to CloudWatch as well\. The CloudWatch logs might contain customer\-sensitive data, such as business data or security information in debug messages\)\. For more information, see [Monitoring AWS Mainframe Modernization with Amazon CloudWatch](monitoring-cloudwatch.md)\.

In addition, if you choose to attach one or more Amazon Elastic File System or Amazon FSx file systems to your runtime environment, the data within those systems will be stored in AWS\. You will need to clean up that data if you decide to stop using the file systems\.

You can use all available Amazon S3 encryption options to secure your data when you place it in the Amazon S3 bucket that AWS Mainframe Modernization uses for application deployment and dataset imports\. In addition, you can use the Amazon EFS and Amazon FSx encryption options if you attach one or more of these file systems to your runtime environment\.

## Encryption at rest<a name="encryption-rest"></a>

AWS Mainframe Modernization integrates with AWS Key Management Service to provide transparent server side encryption \(SSE\) on all dependent resources that store data permanently; namely Amazon S3, Amazon DynamoDB, and Amazon EBS\. AWS Mainframe Modernization creates and manages symmetric encryption AWS KMS keys for you in AWS Key Management Service\. You can also provide your own customer managed key for AWS Mainframe Modernization applications and runtime environments to encrypt Amazon S3 and Amazon EBS resources\. DynamoDB resources are always encrypted using an AWS managed key in the AWS Mainframe Modernization service account\. You cannot encrypt DynamoDB resources using a customer managed key\.

When you create a AWS Mainframe Modernization application, you can provide your customer managed key as an optional parameter\. AWS Mainframe Modernization uses this key to encrypt your application definition as well as other application resources, like JCL files, which are saved in the Amazon S3 bucket that is created in the service’s account\. For more information, see [Create an application ](applications-m2-create.md#applications-m2-create-console)\.

When you create a AWS Mainframe Modernization runtime environment, you can also provide your customer managed key as an optional parameter\. AWS Mainframe Modernization then uses this key to encrypt the Amazon EBS volume that it creates and attaches to your AWS Mainframe Modernization Amazon EC2 instance, which is also in the service’s account\. For more information, see [Create a runtime environment](create-environments-m2.md#create-environments-m2.console)\.

AWS Mainframe Modernization uses your customer managed key for the following tasks:
+ Redeploying an application\.
+ Replacing a AWS Mainframe Modernization Amazon EC2 instance\.
+ Encrypting Amazon Relational Database Service or Amazon Aurora databases, Amazon Simple Queue Service queues, and Amazon ElastiCache caches that are created to support a AWS Mainframe Modernization application\.

To add your customer managed key when you create a runtime environment, specify the `kms-key-id` parameter, as follows:

```
aws m2 create-environment —engine-type microfocus —instance-type M2.m5.large 
--publicly-accessible —engine-version 7.0.3 —name test
--high-availability-config desiredCapacity=2
--kms-key-id myAwesomeKey
```

To add your customer managed key when you create an application, specify the `kms-key-id` parameter, as follows:

```
aws m2 create-application —name test-application —description my description
--engine-type microfocus 
--definition content="$(jq -c . raw-template.json | jq -R)"
--kms-key-id mySecuredKey
```

AWS Mainframe Modernization uses AWS KMS grants to decrypt your secrets stored in Secrets Manager and in workflows such as redeploying an application, replacing a AWS Mainframe Modernization\-managed Amazon EC2 instance, and scaling out a runtime environment\. The grant that AWS Mainframe Modernization creates supports the following operations:
+ Decrypt
+ Encrypt
+ ReEncryptFrom
+ ReEncryptTo
+ GenerateDataKey
+ GenerateDataKeyWithoutPlaintext
+ DescribeKey
+ CreateGrant

AWS Mainframe Modernization collects user application configurations \(JSON files\) and artifacts \(binaries and executables\)\. It also creates metadata that tracks various entities used for the operation of AWS Mainframe Modernization, and creates logs and metrics\. The logs and metrics that are customer\-visible include:
+ CloudWatch logs that reflect application and runtime engine \(either Blu Age or Micro Focus\)\.
+ CloudWatch metrics for operation dashboards\.

In addition, AWS Mainframe Modernization collects usage data and metrics for metering, activity reporting, and so on about the services\. This data is not customer\-visible\.

AWS Mainframe Modernization stores this data in different places depending on the type of data\. Customer data that you upload is stored in an Amazon S3 bucket\. Service data is stored in both Amazon S3 and DynamoDB\. When you deploy an application, both your data and service data are downloaded onto Amazon EBS volumes\. If you choose to attach Amazon EFS or Amazon FSx storage to your runtime environment, data stored in those file systems is also downloaded to the Amazon EBS volume\.

 Encryption at rest is configured by default\. You cannot disable it or change it\. Currently, you cannot change its configuration either\.

## Encryption in transit<a name="encryption-transit"></a>

For interactive applications that are part of transactional workloads, the data exchanges between the terminal emulator and the AWS Mainframe Modernization service endpoint for TN3270 protocol are not encrypted in transit\. If the application requires encryption in transit, you might want to implement some additional tunneling mechanisms\.

AWS Mainframe Modernization uses HTTPS to encrypt the service APIs\. All other communication within AWS Mainframe Modernization is protected by the service VPC or security group, as well as HTTPS\. AWS Mainframe Modernization transfers application artifacts, configurations, and application data\. Application artifacts are copied from an Amazon S3 bucket that you own, as are application data\. You can provide application configurations using a link to Amazon S3 or by uploading a file locally\.

Basic encryption in transit is configured by default, but does not apply to the TN3270 protocol\. AWS Mainframe Modernization uses HTTPS for API endpoints, which are also configured by default\.