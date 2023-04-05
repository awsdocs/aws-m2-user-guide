# Data protection in AWS Mainframe Modernization<a name="data-protection"></a>

The AWS [shared responsibility model](http://aws.amazon.com/compliance/shared-responsibility-model/) applies to data protection in AWS Mainframe Modernization\. As described in this model, AWS is responsible for protecting the global infrastructure that runs all of the AWS Cloud\. You are responsible for maintaining control over your content that is hosted on this infrastructure\. This content includes the security configuration and management tasks for the AWS services that you use\. For more information about data privacy, see the [Data Privacy FAQ](http://aws.amazon.com/compliance/data-privacy-faq)\. For information about data protection in Europe, see the [AWS Shared Responsibility Model and GDPR](http://aws.amazon.com/blogs/security/the-aws-shared-responsibility-model-and-gdpr/) blog post on the *AWS Security Blog*\.

For data protection purposes, we recommend that you protect AWS account credentials and set up individual users with AWS IAM Identity Center \(successor to AWS Single Sign\-On\) or AWS Identity and Access Management \(IAM\)\. That way, each user is given only the permissions necessary to fulfill their job duties\. We also recommend that you secure your data in the following ways:
+ Use multi\-factor authentication \(MFA\) with each account\.
+ Use SSL/TLS to communicate with AWS resources\. We recommend TLS 1\.2 or later\.
+ Set up API and user activity logging with AWS CloudTrail\.
+ Use AWS encryption solutions, along with all default security controls within AWS services\.
+ Use advanced managed security services such as Amazon Macie, which assists in discovering and securing sensitive data that is stored in Amazon S3\.
+ If you require FIPS 140\-2 validated cryptographic modules when accessing AWS through a command line interface or an API, use a FIPS endpoint\. For more information about the available FIPS endpoints, see [Federal Information Processing Standard \(FIPS\) 140\-2](http://aws.amazon.com/compliance/fips/)\.

We strongly recommend that you never put confidential or sensitive information, such as your customers' email addresses, into tags or free\-form text fields such as a **Name** field\. This includes when you work with AWS Mainframe Modernization or other AWS services using the console, API, AWS CLI, or AWS SDKs\. Any data that you enter into tags or free\-form text fields used for names may be used for billing or diagnostic logs\. If you provide a URL to an external server, we strongly recommend that you do not include credentials information in the URL to validate your request to that server\.



## Data that AWS Mainframe Modernization collects<a name="data-protection-types"></a>

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

## Data encryption at rest for AWS Mainframe Modernization service<a name="encryption-rest"></a>

AWS Mainframe Modernization integrates with AWS Key Management Service to provide transparent server side encryption \(SSE\) on all dependent resources that store data permanently; namely Amazon Simple Storage Service, Amazon DynamoDB, and Amazon Elastic Block Store\. AWS Mainframe Modernization creates and manages symmetric encryption AWS KMS keys for you in AWS KMS\.

Encryption of data at rest by default helps reduce the operational overhead and complexity involved in protecting sensitive data\. At the same time, it enables you to migrate applications that require strict encryption compliance and regulatory requirements\.

Although you can't disable this layer of encryption or select an alternate encryption type, you can add a second layer of encryption by choosing a customer managed key when you create runtime environments, applications, and deployments\.

You can use your own customer managed key for AWS Mainframe Modernization applications and runtime environments to encrypt Amazon S3 and Amazon EBS resources\.

For your AWS Mainframe Modernization applications, you can use this key to encrypt your application definition as well as other application resources, like JCL files, which are saved in the Amazon S3 bucket that is created in the service’s account\. For more information, see [Create an application ](applications-m2-create.md#applications-m2-create-console)\.

For your AWS Mainframe Modernization runtime environments, AWS Mainframe Modernization uses your customer managed key to encrypt the Amazon EBS volume that it creates and attaches to your AWS Mainframe Modernization Amazon EC2 instance, which is also in the service’s account\. For more information, see [Create a runtime environment](create-environments-m2.md#create-environments-m2.console)\.

**Note**  
DynamoDB resources are always encrypted using an AWS managed key in the AWS Mainframe Modernization service account\. You cannot encrypt DynamoDB resources using a customer managed key\.

AWS Mainframe Modernization uses your customer managed key for the following tasks:
+ Redeploying an application\.
+ Replacing a AWS Mainframe Modernization Amazon EC2 instance\.
+ Encrypting Amazon Relational Database Service or Amazon Aurora databases, Amazon Simple Queue Service queues, and Amazon ElastiCache caches that are created to support a AWS Mainframe Modernization application\.

For more information, see [Customer managed keys](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#customer-cmk) in the *AWS Key Management Service Developer Guide*\.

The following table summarizes how AWS Mainframe Modernization encrypts your sensitive data\.


| Data type | AWS managed key encryption | Customer managed key encryption | 
| --- | --- | --- | 
|  `Definition` Contains the definition for a particular application\.  |  Enabled  |  Enabled  | 
|  `EnvironmentSummary` Contains information about the runtime environment\.  |  Enabled  |  Enabled  | 
|  `ApplicationSummary` Contains information about the AWS Mainframe Modernization application\.  |  Enabled  |  Enabled  | 
|  `DeploymentSummary` Contains information about a deployment of an AWS Mainframe Modernization application\.  |  Enabled  |  Enabled  | 

**Note**  
AWS Mainframe Modernization automatically enables encryption at rest using AWS managed keys to protect your sensitive data at no charge\. However, AWS KMS charges apply for using a customer managed key\. For more information about pricing, see [AWS Key Management Service Pricing](http://aws.amazon.com/kms/pricing/)\.

For more information on AWS KMS, see [AWS Key Management Service](https://docs.aws.amazon.com/kms/latest/developerguide/overview.html)

## How AWS Mainframe Modernization uses grants in AWS KMS<a name="encryption-rest-grant"></a>

AWS Mainframe Modernization requires a [grant](https://docs.aws.amazon.com/kms/latest/developerguide/grants.html) to use your customer managed key\.

When you create an application or runtime environment, or deploy an application in AWS Mainframe Modernization encrypted with a customer managed key, AWS Mainframe Modernization creates a grant on your behalf by sending a [CreateGrant](https://docs.aws.amazon.com/kms/latest/APIReference/API_CreateGrant.html) request to AWS KMS\. Grants in AWS KMS are used to give AWS Mainframe Modernization access to a KMS key in a customer account\.

AWS Mainframe Modernization requires the grant to use your customer managed key for the following internal operations:
+ Send [DescribeKey](https://docs.aws.amazon.com/kms/latest/APIReference/API_DescribeKey.html) requests to AWS KMS to verify that the symmetric customer managed key ID entered when creating an application, runtime environment, or application deployment is valid\.
+ Send [GenerateDataKey](https://docs.aws.amazon.com/kms/latest/APIReference/API_GenerateDataKeyWithoutPlaintext.html) requests to AWS KMS to encrypt the Amazon EBS volume attached to Amazon EC2 instances that host AWS Mainframe Modernization runtime environments\.
+ Send [Decrypt](https://docs.aws.amazon.com/kms/latest/APIReference/API_Decrypt.html) requests to AWS KMS to decrypt encrypted content on Amazon EBS\.

AWS Mainframe Modernization uses AWS KMS grants to decrypt your secrets stored in Secrets Manager and in workflows such as creating a runtime environment, creating or redeploying an application, and creating a deployment\. The grants that AWS Mainframe Modernization creates support the following operations:
+ Create or update a runtime environment grant:
  + Decrypt
  + Encrypt
  + ReEncryptFrom
  + ReEncryptTo
  + GenerateDataKey
  + DescribeKey
  + CreateGrant
+ Create or redeploy an application grant:
  + GenerateDataKey
+ Create a deployment grant:
  + Decrypt

You can revoke access to the grant, or remove the service's access to the customer managed key at any time\. If you do, AWS Mainframe Modernization won't be able to access any of the data encrypted by the customer managed key, which affects operations that depend on the data\. For example, if AWS Mainframe Modernization tried to access an application definition encrypted by a customer managed key without the grant to that key, the application creation operation would fail\.

AWS Mainframe Modernization collects user application configurations \(JSON files\) and artifacts \(binaries and executables\)\. It also creates metadata that tracks various entities used for the operation of AWS Mainframe Modernization, and creates logs and metrics\. The logs and metrics that are customer\-visible include:
+ CloudWatch logs that reflect application and runtime engine \(either Blu Age or Micro Focus\)\.
+ CloudWatch metrics for operation dashboards\.

In addition, AWS Mainframe Modernization collects usage data and metrics for metering, activity reporting, and so on about the services\. This data is not customer\-visible\.

AWS Mainframe Modernization stores this data in different places depending on the type of data\. Customer data that you upload is stored in an Amazon S3 bucket\. Service data is stored in both Amazon S3 and DynamoDB\. When you deploy an application, both your data and service data are downloaded onto Amazon EBS volumes\. If you choose to attach Amazon EFS or Amazon FSx storage to your runtime environment, data stored in those file systems is also downloaded to the Amazon EBS volume\.

 Encryption at rest is configured by default\. You cannot disable it or change it\. Currently, you cannot change its configuration either\.

## Create a customer managed key<a name="encryption-rest-create-cust-key"></a>

You can create a symmetric customer managed key by using the AWS Management Console or the AWS KMS APIs\.

**To create a symmetric customer managed key**

Follow the steps for [Creating symmetric customer managed key](https://docs.aws.amazon.com/kms/latest/developerguide/create-keys.html#create-symmetric-cmk) in the *AWS Key Management Service Developer Guide*\.

**Key policy**

Key policies control access to your customer managed key\. Every customer managed key must have exactly one key policy, which contains statements that determine who can use the key and how they can use it\. When you create your customer managed key, you can specify a key policy\. For more information, see [Managing access to customer managed keys](https://docs.aws.amazon.com/kms/latest/developerguide/control-access-overview.html#managing-access) in the *AWS Key Management Service Developer Guide*\.

To use your customer managed key with your AWS Mainframe Modernization resources, the following API operations must be permitted in the key policy:
+ `[kms:CreateGrant](https://docs.aws.amazon.com/kms/latest/APIReference/API_CreateGrant.html)` – Adds a grant to a customer managed key\. Grants control access to a specified KMS key, which allows access to [grant operations](https://docs.aws.amazon.com/kms/latest/developerguide/grants.html#terms-grant-operations) AWS Mainframe Modernization requires\. For more information about [Using Grants](https://docs.aws.amazon.com/kms/latest/developerguide/grants.html), see the *AWS Key Management Service Developer Guide*\.

  This allows AWS Mainframe Modernization to do the following:
  + Call `GenerateDataKey` to generate an encrypted data key and store it, because the data key isn't immediately used to encrypt\.
  + Call `Decrypt` to use the stored encrypted data key to access encrypted data\.
  + Set up a retiring principal to allow the service to `RetireGrant`\.
+ `[kms:DescribeKey](https://docs.aws.amazon.com/kms/latest/APIReference/API_DescribeKey.html)` – Provides the customer managed key details to allow AWS Mainframe Modernization to validate the key\.

AWS Mainframe Modernization requires `kms:CreateGrant` and `kms:DescribeKey` permissions in the customer's key policy\. AWS Mainframe Modernization uses this policy to create a grant for itself\.

```
{
    "Version": "2012-10-17",
    "Statement": [{
        "Sid": "Enable IAM User Permissions",
        "Effect": "Allow",
        "Principal": {
            "AWS": "arn:aws:iam::AccountId:role/ExampleRole"
        },
        "Action": [
            "kms:CreateGrant",
            "kms:DescribeKey"
        ],
        "Resource": "*"
    }]
}
```

For more information about [specifying permissions in a policy](https://docs.aws.amazon.com/kms/latest/developerguide/control-access-overview.html#overview-policy-elements), see the *AWS Key Management Service Developer Guide*\.

For more information about [troubleshooting key access](https://docs.aws.amazon.com/kms/latest/developerguide/policy-evaluation.html#example-no-iam), see the *AWS Key Management Service Developer Guide*\.

## Specifying a customer managed key for AWS Mainframe Modernization<a name="encryption-rest-specify-cust-key"></a>

You can specify a customer managed key as a second layer encryption for the following resources:
+ Application
+ Deployment
+ Environment

When you create a resource, you can specify the key by entering a **KMS ID**, which AWS Mainframe Modernization uses to encrypt the sensitive data stored by the resource\.
+ **KMS ID**— A [ key identifier](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#key-id) for a customer managed key\. Enter a key ID, key ARN, alias name, or alias ARN\.

You can specify a customer managed key using the AWS Management Console or the AWS CLI\.

To specify your customer managed key when creating a runtime environment in the AWS Management Console, see [Create an AWS Mainframe Modernization runtime environment](create-environments-m2.md)\. To specify your customer managed key when creating an application in the AWS Management Console, see [Create an AWS Mainframe Modernization application](applications-m2-create.md)\.

To specify your customer managed key when you create a runtime environment with the AWS CLI, specify the `kms-key-id` parameter, as follows:

```
aws m2 create-environment —engine-type microfocus —instance-type M2.m5.large 
--publicly-accessible —engine-version 7.0.3 —name test
--high-availability-config desiredCapacity=2
--kms-key-id myEnvironmentKey
```

To add your customer managed key when you create an application with the AWS CLI, specify the `kms-key-id` parameter, as follows:

```
aws m2 create-application —name test-application —description my description
--engine-type microfocus 
--definition content="$(jq -c . raw-template.json | jq -R)"
--kms-key-id myApplicationKey
```

## AWS Mainframe Modernization encryption context<a name="encryption-rest-context"></a>

An [encryption context](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#encrypt_context) is an optional set of key\-value pairs that contain additional contextual information about the data\.

AWS KMS uses the encryption context as [additional authenticated data](https://docs.aws.amazon.com/crypto/latest/userguide/cryptography-concepts.html#term-aad) to support [authenticated encryption](https://docs.aws.amazon.com/crypto/latest/userguide/cryptography-concepts.html#define-authenticated-encryption)\. When you include an encryption context in a request to encrypt data, AWS KMS binds the encryption context to the encrypted data\. To decrypt data, you include the same encryption context in the request\.

**AWS Mainframe Modernization encryption context**

### <a name="m2-encryption-context"></a>

AWS Mainframe Modernization uses the same encryption context in all AWS KMS cryptographic operations, where the key is `aws:m2:app` and the value is the unique identifier of the application\.

**Example**  

```
"encryptionContextSubset": {
                "aws:m2:app": "a1bc2defabc3defabc4defabcd"
            }
```

**Using encryption context for monitoring**

When you use a symmetric customer managed key to encrypt your applications or runtime environments, you can also use the encryption context in audit records and logs to identify how the customer managed key is being used\. 

**Using encryption context to control access to your customer managed key**

You can use the encryption context in key policies and IAM policies as `conditions` to control access to your symmetric customer managed key\. You can also use encryption context constraints in a grant\.

AWS Mainframe Modernization uses an encryption context constraint in grants to control access to the customer managed key in your account or region\. The grant constraint requires that the operations that the grant allows use the specified encryption context\. The following example shows a request to `CreateGrant` during the `CreateApplication` workflow that uses an encryption context constraint\.

```
//This grant is retired immediately after create application finish
{
   "grantee-principal": m2.us-west-2.amazonaws.com,
   "retiring-principal": m2.us-west-2.amazonaws.com,
   "operations": [
       "GenerateDataKey"
   ]
   "condition": {
        "encryptionContextSubset": {
            “aws:m2:app”: “a1bc2defabc3defabc4defabcd”
   }
}
```

## Monitoring your encryption keys for AWS Mainframe Modernization<a name="m2-encryption-rest-monitor"></a>

When you use an AWS KMS customer managed key with your AWS Mainframe Modernization resources, you can use [AWS CloudTrail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-user-guide.html) or [Amazon CloudWatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/WhatIsCloudWatchLogs.html) to track requests that AWS Mainframe Modernization sends to AWS KMS\.

### Examples for runtime environments<a name="m2-encryption-rest-monitor-env"></a>

The following examples are AWS CloudTrail events for `DescribeKey`, `CreateGrant`, `GenerateDataKey`, and `Decrypt` to monitor KMS operations called by AWS Mainframe Modernization to access data encrypted by your customer managed key:

------
#### [ DescribeKey ]

AWS Mainframe Modernization uses the `DescribeKey` operation to verify if the AWS KMS customer managed key associated with your runtime environment exists in the account and region\.

The following example event records the `DescribeKey` operation:

```
{
    "eventVersion": "1.08",
    "userIdentity": {
        "type": "AssumedRole",
        "principalId": "AROAIGDTESTANDEXAMPLE:Sampleuser01",
        "arn": "arn:aws:sts::111122223333:assumed-role/Admin/Sampleuser01",
        "accountId": "111122223333",
        "accessKeyId": "AKIAIOSFODNN7EXAMPLE3",
        "sessionContext": {
            "sessionIssuer": {
                "type": "Role",
                "principalId": "AROAIGDTESTANDEXAMPLE:Sampleuser01",
                "arn": "arn:aws:sts::111122223333:assumed-role/Admin/Sampleuser01",
                "accountId": "111122223333",
                "userName": "Admin"
            },
            "webIdFederationData": {},
            "attributes": {
                "creationDate": "2022-12-06T19:40:26Z",
                "mfaAuthenticated": "false"
            }
        }
    },
    "eventTime": "2022-12-06T20:23:43Z",
    "eventSource": "kms.amazonaws.com",
    "eventName": "DescribeKey",
    "awsRegion": "us-west-2",
    "sourceIPAddress": "205.251.233.182",
    "userAgent": "ExampleDesktop/1.0 (V1; OS)",
    "requestParameters": {
        "keyId": "00dd0db0-0000-0000-ac00-b0c000SAMPLE"
    },
    "responseElements": null,
    "requestID": "ff000af-00eb-00ce-0e00-ea000fb0fba0SAMPLE",
    "eventID": "ff000af-00eb-00ce-0e00-ea000fb0fba0SAMPLE",
    "readOnly": true,
    "resources": [
        {
            "accountId": "111122223333",
            "type": "AWS::KMS::Key",
            "ARN": "arn:aws:kms:us-west-2:111122223333:key/1234abcd-12ab-34cd-56ef-123456SAMPLE"
        }
    ],
    "eventType": "AwsApiCall",
    "managementEvent": true,
    "recipientAccountId": "111122223333",
    "eventCategory": "Management",
    "tlsDetails": {
        "tlsVersion": "TLSv1.3",
        "cipherSuite": "TLS_AES_256_GCM_SHA384",
        "clientProvidedHostHeader": "kms.us-west-2.amazonaws.com"
    },
    "sessionCredentialFromConsole": "true"
}
```

------
#### [ CreateGrant ]

When you use an AWS KMS customer managed key to encrypt your runtime environment, AWS Mainframe Modernization sends several `CreateGrant` requests on your behalf to perform necessary KMS operations\. Some of the grants that AWS Mainframe Modernization creates are retired immediately after use\. Others are retired when you delete the runtime environment\.

The following example event records the `CreateGrant` operation for the Lambda execution role associated with the Create Environment workflow\. 

```
{
    "eventVersion": "1.08",
    "userIdentity": {
        "type": "AssumedRole",
        "principalId": "AROAIGDTESTANDEXAMPLE:Sampleuser01",
        "arn": "arn:aws:sts::111122223333:assumed-role/Admin/Sampleuser01",
        "accountId": "111122223333",
        "accessKeyId": "AKIAIOSFODNN7EXAMPLE3",
        "sessionContext": {
            "sessionIssuer": {
                "type": "Role",
                "principalId": "AROAIGDTESTANDEXAMPLE:Sampleuser01",
                "arn": "arn:aws:sts::111122223333:assumed-role/Admin/Sampleuser01",
                "accountId": "111122223333",
                "userName": "Admin"
            },
            "webIdFederationData": {},
            "attributes": {
                "creationDate": "2022-12-06T20:11:45Z",
                "mfaAuthenticated": "false"
            }
        },
        "invokedBy": "m2.us-west-2.amazonaws.com"
    },
    "eventTime": "2022-12-06T20:23:09Z",
    "eventSource": "kms.amazonaws.com",
    "eventName": "CreateGrant",
    "awsRegion": "us-west-2",
    "sourceIPAddress": "m2.us-west-2.amazonaws.com",
    "userAgent": "m2.us-west-2.amazonaws.com",
    "requestParameters": {
        "keyId": "arn:aws:kms:us-west-2:111122223333:key/1234abcd-12ab-34cd-56ef-123456SAMPLE",
        "operations": [
            "Encrypt",
            "Decrypt",
            "ReEncryptFrom",
            "ReEncryptTo",
            "GenerateDataKey",
            "GenerateDataKey",
            "DescribeKey",
            "CreateGrant"
        ],
        "granteePrincipal": "m2.us-west-2.amazonaws.com",
        "retiringPrincipal": "m2.us-west-2.amazonaws.com"
    },
    "responseElements": {
        "grantId": "0ab0ac0d0b000f00ea00cc0a0e00fc00bce000c000f0000000c0bc0a0000aaafSAMPLE",
        "keyId": "arn:aws:kms:us-west-2:111122223333:key/1234abcd-12ab-34cd-56ef-123456SAMPLE"
    },
    "requestID": "ff000af-00eb-00ce-0e00-ea000fb0fba0SAMPLE",
    "eventID": "ff000af-00eb-00ce-0e00-ea000fb0fba0SAMPLE",
    "readOnly": false,
    "resources": [
        {
            "accountId": "111122223333",
            "type": "AWS::KMS::Key",
            "ARN": "arn:aws:kms:us-west-2:111122223333:key/1234abcd-12ab-34cd-56ef-123456SAMPLE"
        }
    ],
    "eventType": "AwsApiCall",
    "managementEvent": true,
    "recipientAccountId": "111122223333",
    "eventCategory": "Management"
}
```

The following example event records the `CreateGrant` operation for the Auto Scaling group service\-linked role\. The Lambda execution role associated with the Create Environment workflow calls this `CreateGrant` operation\. It grants permission for the execution role to create a subgrant against the Auto Scaling group's service\-linked role\.

```
{
    "eventVersion": "1.08",
    "userIdentity": {
        "type": "AssumedRole",
        "principalId": "AROA3YPCLM65MZFUPM4JO:EnvironmentWorkflow-alpha-CreateEnvironmentLambda7-HfxDj5zz86tr",
        "arn": "arn:aws:sts::111122223333:assumed-role/EnvironmentWorkflow-alpha-CreateEnvironmentLambdaS-1AU4A8VNQEEKN/EnvironmentWorkflow-alpha-CreateEnvironmentLambda7-HfxDj5zz86tr",
        "accountId": "111122223333",
        "accessKeyId": "AKIAIOSFODNN7EXAMPLE3",
        "sessionContext": {
            "sessionIssuer": {
                "type": "Role",
                "principalId": "AROAIGDTESTANDEXAMPLE:Sampleuser01",
                "arn": "arn:aws:iam::111122223333:role/EnvironmentWorkflow-alpha-CreateEnvironmentLambdaS-1AU4A8VNQEEKN",
                "accountId": "111122223333",
                "userName": "EnvironmentWorkflow-alpha-CreateEnvironmentLambdaS-1AU4A8VNQEEKN"
            },
            "webIdFederationData": {},
            "attributes": {
                "creationDate": "2022-12-06T20:22:28Z",
                "mfaAuthenticated": "false"
            }
        }
    },
    "eventTime": "2022-12-06T20:23:09Z",
    "eventSource": "kms.amazonaws.com",
    "eventName": "CreateGrant",
    "awsRegion": "us-west-2",
    "sourceIPAddress": "54.148.236.160",
    "userAgent": "aws-sdk-java/2.18.21 Linux/4.14.255-276-224.499.amzn2.x86_64 OpenJDK_64-Bit_Server_VM/11.0.14.1+10-LTS Java/11.0.14.1 vendor/Amazon.com_Inc. md/internal exec-env/AWS_Lambda_java11 io/sync http/Apache cfg/retry-mode/legacy",
    "requestParameters": {
        "keyId": "arn:aws:kms:us-west-2:111122223333:key/1234abcd-12ab-34cd-56ef-123456SAMPLE",
        "operations": [
            "Encrypt",
            "Decrypt",
            "ReEncryptFrom",
            "ReEncryptTo",
            "GenerateDataKey",
            "GenerateDataKey",
            "DescribeKey",
            "CreateGrant"
        ],
        "granteePrincipal": "m2.us-west-2.amazonaws.com",
        "retiringPrincipal": "m2.us-west-2.amazonaws.com"
    },
    "responseElements": {
        "grantId": "0ab0ac0d0b000f00ea00cc0a0e00fc00bce000c000f0000000c0bc0a0000aaafSAMPLE",
        "keyId": "arn:aws:kms:us-west-2:111122223333:key/1234abcd-12ab-34cd-56ef-123456SAMPLE"
    },
    "requestID": "ff000af-00eb-00ce-0e00-ea000fb0fba0SAMPLE",
    "eventID": "ff000af-00eb-00ce-0e00-ea000fb0fba0SAMPLE",
    "readOnly": false,
    "resources": [
        {
            "accountId": "111122223333",
            "type": "AWS::KMS::Key",
            "ARN": "arn:aws:kms:us-west-2:111122223333:key/1234abcd-12ab-34cd-56ef-123456SAMPLE"
        }
    ],
    "eventType": "AwsApiCall",
    "managementEvent": true,
    "recipientAccountId": "111122223333",
    "eventCategory": "Management",
    "tlsDetails": {
        "tlsVersion": "TLSv1.3",
        "cipherSuite": "TLS_AES_256_GCM_SHA384",
        "clientProvidedHostHeader": "kms.us-west-2.amazonaws.com"
    }
}
}
```

------
#### [ GenerateDataKey ]

When you enable an AWS KMS customer managed key for your runtime environment resource resource, Auto Scaling creates a unique key for encrypting the Amazon EBS volume associated with the runtime environment\. It sends a `GenerateDataKey` request to AWS KMS that specifies the AWS KMScustomer managed key for the resource\.

The following example event records the `GenerateDataKey` operation:

```
{
    "eventVersion": "1.08",
    "userIdentity": {
        "type": "AssumedRole",
        "principalId": "AROA3YPCLM65EEXVIEH7D:AutoScaling",
        "arn": "arn:aws:sts::111122223333:assumed-role/AWSServiceRoleForAutoScaling/AutoScaling",
        "accountId": "111122223333",
        "accessKeyId": "AKIAIOSFODNN7EXAMPLE3",
        "sessionContext": {
            "sessionIssuer": {
                "type": "Role",
                "principalId": "AROAIGDTESTANDEXAMPLE:Sampleuser01",
                "arn": "arn:aws:iam::111122223333:role/aws-service-role/autoscaling.amazonaws.com/AWSServiceRoleForAutoScaling",
                "accountId": "111122223333",
                "userName": "AWSServiceRoleForAutoScaling"
            },
            "webIdFederationData": {},
            "attributes": {
                "creationDate": "2022-12-06T20:23:16Z",
                "mfaAuthenticated": "false"
            }
        },
        "invokedBy": "autoscaling.amazonaws.com"
    },
    "eventTime": "2022-12-06T20:23:18Z",
    "eventSource": "kms.amazonaws.com",
    "eventName": "GenerateDataKey",
    "awsRegion": "us-west-2",
    "sourceIPAddress": "autoscaling.amazonaws.com",
    "userAgent": "autoscaling.amazonaws.com",
    "requestParameters": {
        "encryptionContext": {
            "aws:ebs:id": "vol-080f7a32d290807f3"
        },
        "keyId": "arn:aws:kms:us-west-2:111122223333:key/1234abcd-12ab-34cd-56ef-123456SAMPLE",
        "numberOfBytes": 64
    },
    "responseElements": null,
    "requestID": "ff000af-00eb-00ce-0e00-ea000fb0fba0SAMPLE",
    "eventID": "ff000af-00eb-00ce-0e00-ea000fb0fba0SAMPLE",
    "readOnly": true,
    "resources": [
        {
            "accountId": "111122223333",
            "type": "AWS::KMS::Key",
            "ARN": "arn:aws:kms:us-west-2:111122223333:key/1234abcd-12ab-34cd-56ef-123456SAMPLE"
        }
    ],
    "eventType": "AwsApiCall",
    "managementEvent": true,
    "recipientAccountId": "111122223333",
    "eventCategory": "Management"
}
```

------
#### [ Decrypt ]

When you access an encrypted runtime environment, Amazon EBS calls the `Decrypt` operation to use the stored encrypted data key to access the encrypted data\. 

The following example event records the `Decrypt` operation:

```
{
    "eventVersion": "1.08",
    "userIdentity": {
        "type": "AWSService",
        "invokedBy": "ebs.amazonaws.com"
    },
    "eventTime": "2022-12-06T20:23:22Z",
    "eventSource": "kms.amazonaws.com",
    "eventName": "Decrypt",
    "awsRegion": "us-west-2",
    "sourceIPAddress": "ebs.amazonaws.com",
    "userAgent": "ebs.amazonaws.com",
    "requestParameters": {
        "encryptionAlgorithm": "SYMMETRIC_DEFAULT",
        "encryptionContext": {
            "aws:ebs:id": "vol-080f7a32d290807f3"
        }
    },
    "responseElements": null,
    "requestID": "ff000af-00eb-00ce-0e00-ea000fb0fba0SAMPLE",
    "eventID": "ff000af-00eb-00ce-0e00-ea000fb0fba0SAMPLE",
    "readOnly": true,
    "resources": [
        {
            "accountId": "111122223333",
            "type": "AWS::KMS::Key",
            "ARN": "arn:aws:kms:us-west-2:111122223333:key/1234abcd-12ab-34cd-56ef-123456SAMPLE"
        }
    ],
    "eventType": "AwsApiCall",
    "managementEvent": true,
    "recipientAccountId": "111122223333",
    "sharedEventID": "ff000af-00eb-00ce-0e00-ea000fb0fba0SAMPLE",
    "eventCategory": "Management"
}
```

------

### Examples for applications<a name="m2-encryption-rest-monitor-app"></a>

The following examples are AWS CloudTrail events for `CreateGrant` and `GenerateDataKey` to monitor KMS operations called by AWS Mainframe Modernization to access data encrypted by your customer managed key:

------
#### [ CreateGrant ]

When you use an AWS KMS customer managed key to encrypt your application resources, the Lambda execution role sends a `CreateGrant` request on your behalf to access the KMS key in your AWS account\. The grant allows the Lambda execution role to upload customer application resources to Amazon S3 using your customer managed key\. This grant is retired immediately after the application is created\.

The following example event records the `CreateGrant` operation:

```
{
    "eventVersion": "1.08",
    "userIdentity": {
        "type": "AssumedRole",
        "principalId": "AROAIGDTESTANDEXAMPLE:Sampleuser01",
        "arn": "arn:aws:sts::111122223333:assumed-role/Admin/Sampleuser01",
        "accountId": "111122223333",
        "accessKeyId": "AKIAIOSFODNN7EXAMPLE3",
        "sessionContext": {
            "sessionIssuer": {
                "type": "Role",
                "principalId": "AROAIGDTESTANDEXAMPLE:Sampleuser01",
                "arn": "arn:aws:sts::111122223333:assumed-role/Admin/Sampleuser01",
                "accountId": "111122223333",
                "userName": "Admin"
            },
            "webIdFederationData": {},
            "attributes": {
                "creationDate": "2022-12-06T21:51:45Z",
                "mfaAuthenticated": "false"
            }
        },
        "invokedBy": "m2.us-west-2.amazonaws.com"
    },
    "eventTime": "2022-12-06T22:47:04Z",
    "eventSource": "kms.amazonaws.com",
    "eventName": "CreateGrant",
    "awsRegion": "us-west-2",
    "sourceIPAddress": "m2.us-west-2.amazonaws.com",
    "userAgent": "m2.us-west-2.amazonaws.com",
    "requestParameters": {
        "keyId": "arn:aws:kms:us-west-2:111122223333:key/1234abcd-12ab-34cd-56ef-123456SAMPLE",
        "constraints": {
            "encryptionContextSubset": {
                "aws:m2:app": "a1bc2defabc3defabc4defabcd"
            }
        },
        "retiringPrincipal": "m2.us-west-2.amazonaws.com",
        "operations": [
            "GenerateDataKey"
        ],
        "granteePrincipal": "m2.us-west-2.amazonaws.com"
    },
    "responseElements": {
        "grantId": "0ab0ac0d0b000f00ea00cc0a0e00fc00bce000c000f0000000c0bc0a0000aaafSAMPLE",
        "keyId": "arn:aws:kms:us-west-2:111122223333:key/1234abcd-12ab-34cd-56ef-123456SAMPLE"
    },
    "requestID": "ff000af-00eb-00ce-0e00-ea000fb0fba0SAMPLE",
    "eventID": "ff000af-00eb-00ce-0e00-ea000fb0fba0SAMPLE",
    "readOnly": false,
    "resources": [
        {
            "accountId": "111122223333",
            "type": "AWS::KMS::Key",
            "ARN": "arn:aws:kms:us-west-2:111122223333:key/1234abcd-12ab-34cd-56ef-123456SAMPLE"
        }
    ],
    "eventType": "AwsApiCall",
    "managementEvent": true,
    "recipientAccountId": "111122223333",
    "eventCategory": "Management"
}
```

------
#### [ GenerateDataKey ]

When you enable an AWS KMS customer managed key for your application resource, the Lambda execution role creates a key that it uses to encrypt and upload customer data to Amazon Simple Storage Service\. The Lambda execution role sends a `GenerateDataKey` request to AWS KMS that specifies the AWS KMScustomer managed key for the resource\.

The following example event records the `GenerateDataKey` operation:

```
{
    "eventVersion": "1.08",
    "userIdentity": {
        "type": "AssumedRole",
        "principalId": "AROA3YPCLM65CLCEKKC7Z:ApplicationWorkflow-alpha-CreateApplicationVersion-CstWZUn5R4u6",
        "arn": "arn:aws:sts::111122223333:assumed-role/ApplicationWorkflow-alpha-CreateApplicationVersion-1IZRBZYDG20B/ApplicationWorkflow-alpha-CreateApplicationVersion-CstWZUn5R4u6",
        "accountId": "111122223333",
        "accessKeyId": "AKIAIOSFODNN7EXAMPLE3",
        "sessionContext": {
            "sessionIssuer": {
                "type": "Role",
                "principalId": "AROAIGDTESTANDEXAMPLE:Sampleuser01",
                "arn": "arn:aws:iam::111122223333:role/ApplicationWorkflow-alpha-CreateApplicationVersion-1IZRBZYDG20B",
                "accountId": "111122223333",
                "userName": "ApplicationWorkflow-alpha-CreateApplicationVersion-1IZRBZYDG20B"
            },
            "webIdFederationData": {},
            "attributes": {
                "creationDate": "2022-12-06T23:28:32Z",
                "mfaAuthenticated": "false"
            }
        },
        "invokedBy": "m2.us-west-2.amazonaws.com"
    },
    "eventTime": "2022-12-06T23:29:08Z",
    "eventSource": "kms.amazonaws.com",
    "eventName": "GenerateDataKey",
    "awsRegion": "us-west-2",
    "sourceIPAddress": "m2.us-west-2.amazonaws.com",
    "userAgent": "m2.us-west-2.amazonaws.com",
    "requestParameters": {
        "encryptionContext": {
            "aws:m2:app": "a1bc2defabc3defabc4defabcd",
            "aws:s3:arn": "arn:aws:s3:::supernova-processedtemplate-111122223333-us-west-2/111122223333/a1bc2defabc3defabc4defabcd/1/cics-transaction/ZBNKE35.so"
        },
        "keySpec": "AES_256",
        "keyId": "arn:aws:kms:us-west-2:111122223333:key/1234abcd-12ab-34cd-56ef-123456SAMPLE"
    },
    "responseElements": null,
    "requestID": "ff000af-00eb-00ce-0e00-ea000fb0fba0SAMPLE",
    "eventID": "ff000af-00eb-00ce-0e00-ea000fb0fba0SAMPLE",
    "readOnly": true,
    "resources": [
        {
            "accountId": "111122223333",
            "type": "AWS::KMS::Key",
            "ARN": "arn:aws:kms:us-west-2:111122223333:key/1234abcd-12ab-34cd-56ef-123456SAMPLE"
        }
    ],
    "eventType": "AwsApiCall",
    "managementEvent": true,
    "recipientAccountId": "111122223333",
    "eventCategory": "Management"
}
```

------

### Examples for deployments<a name="m2-encryption-rest-monitor-deploy"></a>

The following examples are AWS CloudTrail events for `CreateGrant` and `Decrypt` to monitor KMS operations called by AWS Mainframe Modernization to access data encrypted by your customer managed key:

------
#### [ CreateGrant ]

When you use an AWS KMS customer managed key to encrypt your deployment resources, AWS Mainframe Modernization sends two `CreateGrant` requests on your behalf\. The first grant is against the current Lambda execution role to call ListBatchJobScriptFiles, and is retired immediately after deployment finishes\. The second grant is against the Amazon EC2 scoped down instance role so that Amazon EC2 can download customer application resources from Amazon S3\. This grant is retired when the application is deleted from the runtime environment\.

The following example event records the `CreateGrant` operation:

```
{
    "eventVersion": "1.08",
    "userIdentity": {
        "type": "AssumedRole",
        "principalId": "AROAIGDTESTANDEXAMPLE:Sampleuser01",
        "arn": "arn:aws:sts::111122223333:assumed-role/Admin/Sampleuser01",
        "accountId": "111122223333",
        "accessKeyId": "AKIAIOSFODNN7EXAMPLE3",
        "sessionContext": {
            "sessionIssuer": {
                "type": "Role",
                "principalId": "AROAIGDTESTANDEXAMPLE:Sampleuser01",
                "arn": "arn:aws:sts::111122223333:assumed-role/Admin/Sampleuser01",
                "accountId": "111122223333",
                "userName": "Admin"
            },
            "webIdFederationData": {},
            "attributes": {
                "creationDate": "2022-12-06T21:51:45Z",
                "mfaAuthenticated": "false"
            }
        },
        "invokedBy": "m2.us-west-2.amazonaws.com"
    },
    "eventTime": "2022-12-06T23:40:07Z",
    "eventSource": "kms.amazonaws.com",
    "eventName": "CreateGrant",
    "awsRegion": "us-west-2",
    "sourceIPAddress": "m2.us-west-2.amazonaws.com",
    "userAgent": "m2.us-west-2.amazonaws.com",
    "requestParameters": {
        "operations": [
            "Decrypt"
        ],
        "constraints": {
            "encryptionContextSubset": {
                "aws:m2:app": "a1bc2defabc3defabc4defabcd"
            }
        },
        "granteePrincipal": "m2.us-west-2.amazonaws.com",
        "retiringPrincipal": "m2.us-west-2.amazonaws.com",
        "keyId": "arn:aws:kms:us-west-2:111122223333:key/1234abcd-12ab-34cd-56ef-123456SAMPLE"
    },
    "responseElements": {
        "grantId": "0ab0ac0d0b000f00ea00cc0a0e00fc00bce000c000f0000000c0bc0a0000aaafSAMPLE",
        "keyId": "arn:aws:kms:us-west-2:111122223333:key/1234abcd-12ab-34cd-56ef-123456SAMPLE"
    },
    "requestID": "ff000af-00eb-00ce-0e00-ea000fb0fba0SAMPLE",
    "eventID": "ff000af-00eb-00ce-0e00-ea000fb0fba0SAMPLE",
    "readOnly": false,
    "resources": [
        {
            "accountId": "111122223333",
            "type": "AWS::KMS::Key",
            "ARN": "arn:aws:kms:us-west-2:111122223333:key/1234abcd-12ab-34cd-56ef-123456SAMPLE"
        }
    ],
    "eventType": "AwsApiCall",
    "managementEvent": true,
    "recipientAccountId": "111122223333",
    "eventCategory": "Management"
}
```

------
#### [ Decrypt ]

When you access a deployment, Amazon EC2 calls the `Decrypt` operation to use the stored encrypted data key to decrypt and download encrypted customer data from Amazon S3\. 

The following example event records the `Decrypt` operation:

```
{
    "eventVersion": "1.08",
    "userIdentity": {
        "type": "AssumedRole",
        "principalId": "AROA3YPCLM65BSPZ37E6G:m2-hm-bqe367dxtfcpdbzmnhfzranisu",
        "arn": "arn:aws:sts::111122223333:assumed-role/SupernovaEnvironmentInstanceScopeDownRole/m2-hm-bqe367dxtfcpdbzmnhfzranisu",
        "accountId": "111122223333",
        "accessKeyId": "AKIAIOSFODNN7EXAMPLE3",
        "sessionContext": {
            "sessionIssuer": {
                "type": "Role",
                "principalId": "AROAIGDTESTANDEXAMPLE:Sampleuser01",
                "arn": "arn:aws:iam::111122223333:role/SupernovaEnvironmentInstanceScopeDownRole",
                "accountId": "111122223333",
                "userName": "SupernovaEnvironmentInstanceScopeDownRole"
            },
            "webIdFederationData": {},
            "attributes": {
                "creationDate": "2022-12-06T23:19:29Z",
                "mfaAuthenticated": "false"
            }
        },
        "invokedBy": "m2.us-west-2.amazonaws.com"
    },
    "eventTime": "2022-12-06T23:40:15Z",
    "eventSource": "kms.amazonaws.com",
    "eventName": "Decrypt",
    "awsRegion": "us-west-2",
    "sourceIPAddress": "m2.us-west-2.amazonaws.com",
    "userAgent": "m2.us-west-2.amazonaws.com",
    "requestParameters": {
        "encryptionContext": {
            "aws:m2:app": "a1bc2defabc3defabc4defabcdm",
            "aws:s3:arn": "arn:aws:s3:::supernova-processedtemplate-111122223333-us-west-2/111122223333/a1bc2defabc3defabc4defabcdm/1/cics-transaction/BBANK40P.so"
        },
        "encryptionAlgorithm": "SYMMETRIC_DEFAULT"
    },
    "responseElements": null,
    "requestID": "ff000af-00eb-00ce-0e00-ea000fb0fba0SAMPLE",
    "eventID": "ff000af-00eb-00ce-0e00-ea000fb0fba0SAMPLE",
    "readOnly": true,
    "resources": [
        {
            "accountId": "111122223333",
            "type": "AWS::KMS::Key",
            "ARN": "arn:aws:kms:us-west-2:111122223333:key/1234abcd-12ab-34cd-56ef-123456SAMPLE"
        }
    ],
    "eventType": "AwsApiCall",
    "managementEvent": true,
    "recipientAccountId": "111122223333",
    "eventCategory": "Management"
}
```

------

## Learn more<a name="Learn-more-data-at-rest-encryption"></a>

The following resources provide more information about data encryption at rest\.
+ For more information about [AWS Key Management Service basic concepts](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html), see the *AWS Key Management Service Developer Guide*\.
+ For more information about [Security best practices for AWS Key Management Service](https://docs.aws.amazon.com/kms/latest/developerguide/best-practices.html), see the *AWS Key Management Service Developer Guide*\.

## Encryption in transit<a name="encryption-transit"></a>

For interactive applications that are part of transactional workloads, the data exchanges between the terminal emulator and the AWS Mainframe Modernization service endpoint for TN3270 protocol are not encrypted in transit\. If the application requires encryption in transit, you might want to implement some additional tunneling mechanisms\.

AWS Mainframe Modernization uses HTTPS to encrypt the service APIs\. All other communication within AWS Mainframe Modernization is protected by the service VPC or security group, as well as HTTPS\. AWS Mainframe Modernization transfers application artifacts, configurations, and application data\. Application artifacts are copied from an Amazon S3 bucket that you own, as are application data\. You can provide application configurations using a link to Amazon S3 or by uploading a file locally\.

Basic encryption in transit is configured by default, but does not apply to the TN3270 protocol\. AWS Mainframe Modernization uses HTTPS for API endpoints, which are also configured by default\.