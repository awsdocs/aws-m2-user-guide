# Create AWS resources for a migrated application<a name="applications-m2-other-resources"></a>

In order to run your migrated application in AWS, you must create some AWS resources with other AWS services\. The resources you must create include the following:
+ An S3 bucket to hold application code, configuration, data files, and other required artifacts\.
+ An Amazon RDS or Amazon Aurora database to hold the data that the application requires\.
+ An AWS KMS key, which is required by AWS Secrets Manager to create and store secrets\.
+ A Secrets Manager secret to hold the database credentials\.

**Note**  
Each migrated application requires its own set of these resources\. This is a minimum set\. Your application might also require additional resources, such as Amazon Cognito secrets or MQ queues\.

## Required permissions<a name="applications-m2-other-resources-perms"></a>

Make sure that you have the following permissions:
+ `s3:CreateBucket`, `s3:PutObject`
+ `rds:CreateDBInstance`
+ `kms:CreateKey`
+ `secretsmanager:CreateSecret`

## Amazon S3 bucket<a name="applications-m2-other-resources-bucket"></a>

Both refactored and replatformed applications require an S3 bucket that you configure as follows:

```
bucket-name/root-folder-name/application-name
```

**bucket\-name**  
Any name within the constraints of Amazon S3 naming\. We recommend that you include the AWS Region name as part of your bucket name\. Make sure that you create the bucket in the same Region where you plan to deploy the migrated application\.

**root\-folder\-name**  
Name required to satisfy constraints in the application definition, which you create as part of the AWS Mainframe Modernization application\. You can use the `root-folder-name` to distinguish between different versions of an application, for example, V1 and V2\.

**application\-name**  
The name of your migrated application, for example, PlanetsDemo or BankDemo\.

## Application structure for Blu Age refactored applications<a name="applications-m2-other-resources-structure"></a>

If you use the Blu Age refactoring pattern, the Blu Age runtime engine expects the following structure inside the `application-name` folder in your S3 bucket:

![\[The expected structure within the application-name folder.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/ba-app-structure.png)

**config**  
Contains the YAML files for your project\. These are the YAML files specific to your application, typically named something like `application-planetsdemo.yaml` and not the `application-main.yaml` file that AWS Mainframe Modernization supplies and sets up automatically for you\.

**jics/sql**  
Contains the `initJics.sql` script that initializes the JICS database for your application\.

**scripts**  
Optional\. Contains application scripts, which you can also supply directly inside the `war` files\.

**webapps**  
Contains the `war` files for your application, which are an output of the modernization process\.

## Database<a name="applications-m2-other-resources-database"></a>

Both refactored and replatformed applications might require a database\. You must create, configure, and manage the database according to specific requirements for each runtime engine\. AWS Mainframe Modernization supports encryption in transit on this database\. If you enable SSL on your database, make sure that you specify `sslMode` in the database secret along with the connection details of the database\. For more information, see [AWS Secrets Manager secret](#applications-m2-other-resources-secret)\. 

If you use the Blu Age refactoring pattern, and you need a BluSam database, the Blu Age runtime engine expects an Amazon Aurora PostgreSQL database, which you must create, configure, and manage\. The BluSam database is optional\. Create this database only if your application requires it\. To create the database, follow the steps in [Creating an Amazon Aurora DB cluster](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.CreateInstance.html) in the *Amazon Aurora User Guide*\. 

If you are using the Micro Focus replatforming pattern, you can create either an Amazon RDS or an Amazon Aurora PostgreSQL database\. To create the database, follow the steps in [Creating an Amazon RDS DB instance](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_CreateDBInstance.html) in the *Amazon RDS User Guide* or in [Creating an Amazon Aurora DB cluster](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.CreateInstance.html) in the *Amazon Aurora User Guide*\.

For both runtime engines, you must store the database credentials in AWS Secrets Manager using an AWS KMS key to encrypt them\.

## AWS Key Management Service key<a name="applications-m2-other-resources-key"></a>

You must store the credentials for the application database securely in AWS Secrets Manager\. To create a secret in Secrets Manager, you must create an AWS KMS key\. To create an KMS key, follow the steps in [Creating keys](https://docs.aws.amazon.com/kms/latest/developerguide/create-keys.html) in the *AWS Key Management Service Developer Guide*\.

After you create the key, you must update the key policy to grant AWS Mainframe Modernization decrypt permissions\. Add the following policy statements:

```
{
   "Effect" : "Allow",
   "Principal" : {
   "Service" : "m2.amazonaws.com"
   },
   "Action" : "kms:Decrypt",
   "Resource" : "*"
   }
```

## AWS Secrets Manager secret<a name="applications-m2-other-resources-secret"></a>

You must store the credentials for the application database securely in AWS Secrets Manager\. To create a secret follow the steps in [Create a database secret](https://docs.aws.amazon.com/secretsmanager/latest/userguide/create_database_secret.html) in the *AWS Secrets Manager User Guide*\.

AWS Mainframe Modernization supports encryption in transit on this database\. If you enable SSL on your database, make sure that you specify `sslMode` in the database secret along with the connection details of the database\. You can specify one of the following values for `sslMode`: `verify-full`, `verify-ca`, or `disable`\.

 During the key creation process, choose **Resource permissions \- optional**, and then choose **Edit permissions**\. In the policy editor, add a resource\-based policy, such as the following, to retrieve the content of the encrypted fields\. 

```
 {
   "Effect" : "Allow",
   "Principal" : {
   "Service" : "m2.amazonaws.com"
   },
   "Action" : "secretsmanager:GetSecretValue",
   "Resource" : "*"
   }
```