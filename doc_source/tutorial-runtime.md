# Tutorial: Managed Runtime for Micro Focus<a name="tutorial-runtime"></a>

This tutorial shows how to deploy and run the BankDemo sample application in a AWS Mainframe Modernization managed runtime environment with the Micro Focus runtime engine\. The BankDemo sample application is a simplified banking application similar to what bank employees might use to check customer account balances, transfer funds, or calculate the cost of a loan\. In the tutorial, you create resources in other AWS services, including Amazon Simple Storage Service, Amazon Relational Database Service or Amazon Aurora, AWS Key Management Service, and AWS Secrets Manager\.

**Topics**
+ [Prerequisites](#tutorial-runtime-mf-prerequisites)
+ [Step 1: Create a database](#tutorial-runtime-mf-db)
+ [Step 2: Create and configure an AWS Key Management Service key](#tutorial-runtime-mf-key)
+ [Step 3: Create and configure an AWS Secrets Manager database secret](#tutorial-runtime-mf-secret)
+ [Step 4: Create a runtime environment](#tutorial-runtime-mf-env)
+ [Step 5: Create an application](#tutorial-runtime-mf-app)
+ [Step 6: Deploy an application](#tutorial-runtime-mf-deploy)
+ [Step 7: Import data sets](#tutorial-runtime-mf-import)
+ [Step 8: Start an application](#tutorial-runtime-mf-start)
+ [Step 9: Connect to the BankDemo sample application](#tutorial-runtime-mf-connect)
+ [Clean up resources](#tutorial-runtime-mf-clean)
+ [Next steps](#tutorial-runtime-mf-next)

## Prerequisites<a name="tutorial-runtime-mf-prerequisites"></a>

Before you start the tutorial, make sure you complete the following prerequisites:
+ Download the [BankDemo sample application](https://d1vi4vxke6c2hu.cloudfront.net/demo/bankdemo_runtime.zip), unzip it, and upload the files to a bucket in Amazon S3\. Make sure that the bucket is in the same Region as AWS Mainframe Modernization\. For more information, see the [Tutorial: Setting up the Micro Focus build for the BankDemo sample application](tutorial-build.md)\.
+ Download the [catalog files](https://d1vi4vxke6c2hu.cloudfront.net/demo/catalog.zip) that the BankDemo sample application requires to locate the application data, unzip them, and then upload them to an Amazon S3 bucket, such as `s3://m2-tutorial`\.
+ Make sure that your IAM user has the `CREATEDB` permission\.

## Step 1: Create a database<a name="tutorial-runtime-mf-db"></a>

In this step, you create a PostgreSQL database in either Amazon Relational Database Service or Amazon Aurora\. For the tutorial, this database contains the data sets that the BankDemo sample application uses for customer tasks, such as checking balances or transferring funds\.

**To create a database in Amazon RDS**

1. To create the database, follow the instructions in [Creating a PostgreSQL DB instance and connecting to a database on a PostgreSQL DB instance](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_GettingStarted.CreatingConnecting.PostgreSQL.html) in the *Amazon RDS User Guide*\.

1. After you create the database, configure it by making the following changes:
   + Create a custom parameter group by following the instructions in [Creating a DB parameter group](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_WorkingWithDBInstanceParamGroups.html#USER_WorkingWithParamGroups.Creating)\.
   + Change the `max_prepared_transactions` parameter value to 100 by following the instructions in [Modifying parameters in a DB parameter group](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_WorkingWithDBInstanceParamGroups.html#USER_WorkingWithParamGroups.Modifying)\.
   + Associate your custom parameter group with the database by following the instructions in [Associating a DB parameter group with a DB instance](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_WorkingWithDBInstanceParamGroups.html#USER_WorkingWithParamGroups.Associating)\.

   For more information on parameter groups, see [Working with parameter groups](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_WorkingWithParamGroups.html)\.

**To create a database in Aurora**

1. To create the database, follow the instructions in [Creating a DB cluster and connecting to a database on an Aurora PostgreSQL DB cluster](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/CHAP_GettingStartedAurora.CreatingConnecting.AuroraPostgreSQL.html) in the *Amazon Aurora User Guide*\. 

1. After you create the database, configure it by making the following changes:
   + Create a custom DB cluster parameter group by following the instructions in [Creating a DB cluster parameter group](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/USER_WorkingWithDBClusterParamGroups.html#USER_WorkingWithParamGroups.CreatingCluster)\.
   + Change the `max_prepared_transactions` parameter value to 100 by following the instructions in [Modifying parameters in a DB cluster parameter group](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/USER_WorkingWithDBClusterParamGroups.html#USER_WorkingWithParamGroups.ModifyingCluster)\.
   + Associate your custom parameter group with the database by following the instructions in [Associating a DB cluster parameter group with a DB cluster](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/USER_WorkingWithDBClusterParamGroups.html#USER_WorkingWithParamGroups.AssociatingCluster)\.

   For more information on parameter groups, see [Working with parameter groups](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/USER_WorkingWithParamGroups.html)\. For more information on DB cluster parameter groups, see [Working with DB cluster parameter groups](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/USER_WorkingWithDBClusterParamGroups.html)\.

## Step 2: Create and configure an AWS Key Management Service key<a name="tutorial-runtime-mf-key"></a>

To store the credentials securely for the Amazon RDS or Aurora database instance that you created for this tutorial, you need to create a database secret in AWS Secrets Manager\. To create a database secret in Secrets Manager, you need to create an AWS Key Management Service key\.

To create a AWS KMS key, follow the steps in [Creating keys](https://docs.aws.amazon.com/kms/latest/developerguide/create-keys.html) in the *AWS Key Management Service Developer Guide*\.

To grant AWS Mainframe Modernization decrypt permissions, you must update the key policy\. Add policy statements as shown here:

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

## Step 3: Create and configure an AWS Secrets Manager database secret<a name="tutorial-runtime-mf-secret"></a>

To create a database secret in Secrets Manager for storing the credentials for the database you created previously, follow the steps in [Create a secret](https://docs.aws.amazon.com/secretsmanager/latest/userguide/manage_create-basic-secret.html) in the *AWS Secrets Manager User Guide*\. Specify the key you created in the previous steps for the encryption key\.

During the key creation process, choose **Resource permissions \- optional**, then choose **Edit permissions**\. In the editor, add a resource\-based policy, such as the following, to retrieve the content of the encrypted fields\.

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

Choose **Save**\.

## Step 4: Create a runtime environment<a name="tutorial-runtime-mf-env"></a>

1. Open the [AWS Mainframe Modernization console](https://us-west-2.console.aws.amazon.com/m2/home?region=us-west-2#/)\.  
![\[Modernize mainframe applications with replatform with the replatform pattern selected.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/m2-mf-get-started.png)

1. In **Modernize mainframe applications**, choose **Replatform with Micro Focus**, and then choose **Get started**\.

1. On the **Get started** page, under **How would you like to start with AWS Mainframe Modernization?**, choose **Deploy**\. Then, in the **Deploy** section, choose **Create runtime environment**\.

1. Choose **Continue**\.  
![\[How would you like to start AWS Mainframe Modernization with deploy and create runtime environment selected.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/m2-mf-create-env.png)

1. Specify a name, for example, **Tutorial**, for the runtime environment\. Choose **Next**\.  
![\[The runtime environment name and description section .\]](http://docs.aws.amazon.com/m2/latest/userguide/images/m2-mf-create-env-basic.png)

1. Under **Availability**, choose **High availability cluster**\. Under **Resources**, choose either M2\.c5\.large or M2\.m5\.large for the instance type, and the number of instances \(up to two\) that you want\. Make sure you choose at least two subnets, and choose **Allow applications deployed to this environment to be publicly accessible**\. Then choose **Next**\.  
![\[The specify configurations page with high availability selected. The M2.m5.large instance type is also selected.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/env-config.png)

1. On the **Attach storage** page, choose **Next**\.

1. On the **Review and create** page, review all the configurations that you provided for the runtime environment, and then choose **Create Environment**\.  
![\[The review and create page showing the previous selections.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/env-review.png)

When a banner appears that says `Environment name was created successfully`, then the **Status** field changes to **Available**\. The environment creation process can take up to two minutes\.

![\[The environment created successfully message.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/env-confirm.png)

## Step 5: Create an application<a name="tutorial-runtime-mf-app"></a>

1. In the navigation pane, choose **Applications**\. Then choose **Create application**\.  
![\[The applications page with the Create application shown.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/app-create.png)

1. In **Specify basic information**, enter `BankDemo` for the name and make sure **Micro Focus** is selected\. Then choose **Next**\.  
![\[The create applications page with the Micro Focus engine type selected.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/m2-mf-create-app.png)

1. On the **Specify resources and configurations** page, choose how you want to specify the application definition\. You can use the inline editor or else specify an application definition JSON file in an Amazon S3 bucket\. Paste or type the application definition or provide the Amazon S3 location\. Then choose **Next**\.  
![\[The specify resources and configurations page with a JSON file displayed in the online editor.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/app-config.png)

   You can use the following sample application definition file\. Make sure to replace `$s3_bucket` and `$s3-key-prefix` with the correct values for your Amazon S3 bucket and the folder where you uploaded the BankDemo sample files\.

   ```
   {
       "template-version": "2.0",
       "source-locations": [{
           "source-id": "s3-source",
           "source-type": "s3",
           "properties": {
               "s3-bucket": "my-bankdemo-bucket",
               "s3-key-prefix": "v1"
           }
       }],
       "definition": {
           "listeners": [{
               "port": 6000,
               "type": "TN3270"
           }],
           "dataset-location": {
               "db-locations": [{
                   "name": "Database1",
                   "secret-manager-arn": "arn:aws:secretsmanager:Region:123456789012:secret:PostgreSQL_database-1-n0A0BC"
               }]
           },
           "file-locations": [{
               "name": "Mount1",
               "file-location": "/m2/mount/catalog/"
           }],
           "catalog-location": "${s3-source}/dataset/catalog"
   
       },
       "batch-settings": {
           "initiators": [{
               "classes": ["A", "B"],
               "description": "initiator_AB...."
           }, {
               "classes": ["C", "D"],
               "description": "initiator_CD...."
           }],
           "printers": [{
               "classes": ["A"]
           }],
           "jcl-file-location": "${s3-source}/jcl",
           "binary-file-location": "${s3-source}/batch/binaries",
           "jes-dataset-location": "${s3-source}/batch/jes",
           "Proclib-location": "${s3-source}/batch/procLib"
       },
       "cics-settings": {
           "binary-file-location": "${s3-source}/transaction",
           "csd-file-location": "${s3-source}/RDEF",
           "system-initialization-table": "BNKCICV"
       },
       "xa-resources": [{
           "name": "XASQL",
           "secret-manager-arn": "arn:aws:secretsmanager:Region:123456789012:secret:PostgreSQL_database-1-n0A0BC",
           "module": "${s3-source}/xa/ESPGSQLXA64.so"
       }]
   }
   ```
**Note**  
This file is subject to change\.

   For more information on the application definition, see [Micro Focus application definition](applications-m2-definition.md#applications-m2-definition-mf)\.

1. On the **Review and create** page, review the information that you provided, and choose **Create application**\.  
![\[The Review and create page showing the previous selections.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/app-review.png)

When a banner appears that says `Application name was created successfully`, then the **Status** field changes to **Available**\.

![\[The Application created successfully message.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/app-confirm.png)

## Step 6: Deploy an application<a name="tutorial-runtime-mf-deploy"></a>

1. In the navigation pane, choose **Applications**, and then choose **BankDemo**\. Choose **Actions**, and then choose **Deploy application**\.  
![\[The BankDemo application details page with Deploy application selected on the Actions menu.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/deploy-action.png)

1. Choose the environment that you created previously, for example, **Tutorial**, and then choose **Deploy**\.  
![\[The Deploy Application page with the Tutorial environment selected.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/deploy-env.png)

When the BankDemo application deploys successfully, the status changes to **Ready**\.

![\[The applications page showing the Ready status.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/deploy-confirm.png)

## Step 7: Import data sets<a name="tutorial-runtime-mf-import"></a>

1. In the navigation pane, choose **Applications**, and then choose **BankDemo**\. Choose the **Data sets** tab\. Then choose how you want to specify the data sets\. You can use a data set configuration JSON file in an Amazon S3 bucket, or specify the data set configuration values separately\.

1. Choose **Add new item**\. Add each data set and choose **Submit** after you provide the information for each data set\. 

   The following table lists the data sets that you must import\. Replace `$S3_DATASET_PREFIX` with your Amazon S3 bucket that contains the catalog data, for example, `S3_DATASET_PREFIX=s3://m2-tutorial/catalog`\.
**Note**  
Make sure you specify the Amazon S3 bucket name, not the bucket ARN\. Do not specify an absolute path to resources in the bucket\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/m2/latest/userguide/tutorial-runtime.html)

   Alternatively, you can specify the data set configuration in a JSON file, such as the following\. This file shows all the required data sets\. Replace `$S3_DATASET_PREFIX` with your Amazon S3 bucket that contains the catalog data, for example, `m2-tutorial/catalog`\.

   ```
   {
       "dataSets": [{
               "dataSet": {
                   "storageType": "Database",
                   "datasetName": "MFI01V.MFIDEMO.BNKACC",
                   "relativePath": "DATA",
                   "datasetOrg": {
                       "vsam": {
                           "format": "KS",
                           "encoding": "A",
                           "primaryKey": {
                               "length": 9,
                               "offset": 5
                           }
                       }
                   },
                   "recordLength": {
                       "min": 200,
                       "max": 200
                   }
               },
               "externalLocation": {
                   "s3Location": "$S3_DATASET_PREFIX/data/MFI01V.MFIDEMO.BNKACC.DAT"
               }
           },
           {
               "dataSet": {
                   "storageType": "Database",
                   "datasetName": "MFI01V.MFIDEMO.BNKATYPE",
                   "relativePath": "DATA",
                   "datasetOrg": {
                       "vsam": {
                           "format": "KS",
                           "encoding": "A",
                           "primaryKey": {
                               "length": 1,
                               "offset": 0
                           }
                       }
                   },
                   "recordLength": {
                       "min": 100,
                       "max": 100
                   }
               },
               "externalLocation": {
                   "s3Location": "$S3_DATASET_PREFIX/data/MFI01V.MFIDEMO.BNKATYPE.DAT"
               }
           },
           {
               "dataSet": {
                   "storageType": "Database",
                   "datasetName": "MFI01V.MFIDEMO.BNKCUST",
                   "relativePath": "DATA",
                   "datasetOrg": {
                       "vsam": {
                           "format": "KS",
                           "encoding": "A",
                           "primaryKey": {
                               "length": 5,
                               "offset": 0
                           }
                       }
                   },
                   "recordLength": {
                       "min": 250,
                       "max": 250
                   }
               },
               "externalLocation": {
                   "s3Location": "$S3_DATASET_PREFIX/data/MFI01V.MFIDEMO.BNKCUST.DAT"
               }
           },
           {
               "dataSet": {
                   "storageType": "Database",
                   "datasetName": "MFI01V.MFIDEMO.BNKHELP",
                   "relativePath": "DATA",
                   "datasetOrg": {
                       "vsam": {
                           "format": "KS",
                           "encoding": "A",
                           "primaryKey": {
                               "length": 8,
                               "offset": 0
                           }
                       }
                   },
                   "recordLength": {
                       "min": 83,
                       "max": 83
                   }
               },
               "externalLocation": {
                   "s3Location": "$S3_DATASET_PREFIX/data/MFI01V.MFIDEMO.BNKHELP.DAT"
               }
           },
           {
               "dataSet": {
                   "storageType": "Database",
                   "datasetName": "MFI01V.MFIDEMO.BNKTXN",
                   "relativePath": "DATA",
                   "datasetOrg": {
                       "vsam": {
                           "format": "KS",
                           "encoding": "A",
                           "primaryKey": {
                               "length": 16,
                               "offset": 26
                           }
                       }
                   },
                   "recordLength": {
                       "min": 400,
                       "max": 400
                   }
               },
               "externalLocation": {
                   "s3Location": "$S3_DATASET_PREFIX/data/MFI01V.MFIDEMO.BNKTXN.DAT"
               }
           }
   
       ]
   }
   ```

When AWS Mainframe Modernization finishes importing the data sets, the **Status** field changes to **Completed**\. Wait until the number of failed records is zero\.

![\[The BankDemo application page with the Data sets tab selected and the completed status displayed.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/import-confirm.png)

## Step 8: Start an application<a name="tutorial-runtime-mf-start"></a>
+ In the navigation pane, choose **Applications**, and then choose **BankDemo**\. Choose **Actions**, and then choose **Start application**\.  
![\[The BankDemo application page with the Definition tab selected and Start application selected on the Actions menu.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/start-action.png)

When the BankDemo application starts to run successfully, the **Status** field changes to **Running**\.

![\[The Application start succeeded message.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/start-confirm.png)

## Step 9: Connect to the BankDemo sample application<a name="tutorial-runtime-mf-connect"></a>

Before you connect, make sure that the VPC and security group that you specified for the application are the same as the ones that you applied to your network interface\.

To configure the TN3270 connection, you also need the DNS hostname of the network interface\. To locate the DNS hostname for configuration, complete the following steps:

1. Open the AWS Mainframe Modernization console and choose **Applications**\.

1. In the list of applications, choose the application you want to display the details page\.

1. Note the string of characters under **DNS Hostname**\.  
![\[The BankDemo application page with the Definition tab selected and the ARN and DNS Hostname fields shown.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/m2-dns-hostname.png)

**Note**  
To ensure that the Network Load Balancer doesn’t silently drop your connection, configure the keepalive setting on your TN3270 terminal to at least 180 seconds\.

Continue to connect to the BankDemo sample application, as follows:

1. Start the terminal emulator you want\. This tutorial uses Micro Focus Rumba\+\.  
![\[The terminal emulator welcome screen.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/connect-start.png)

1. Choose **Connection**, then **Configure**, then **TN3270**\.  
![\[The Connection menu with the Configure option selected.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/connect-config-1.png)

   To add the DNS hostname address you noted earlier, choose **Insert**, and specify `6000` for the **Telnet Port**\.  
![\[The Session Configuration page with the DNS hostname added.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/connect-config-2.png)

1. Enter the BANK transaction name\.  
![\[The terminal emulator screen with the BANK transaction name entered.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/connect-name.png)

1. Type `B0001` for the username and `A` for the password\.  
![\[The sample application login screen with B0001 entered for the username.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/connect-login.png)

1. After you log in successfully, you can navigate through the BankDemo application\.  
![\[The BankDemo application main screen.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/connect-nav-1.png)  
![\[The BankDemo application account balance screen.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/connect-nav-2.png)

## Clean up resources<a name="tutorial-runtime-mf-clean"></a>

If you no longer need the resources that you created for this tutorial, delete them to avoid additional charges\. To do so, complete the following steps:
+ If necessary, stop the BankDemo application\.
+ Delete the application\. For more information, see [Delete a AWS Mainframe Modernization application](applications-m2-delete.md)\.
+ Delete the runtime environment\. For more information, see [Delete a AWS Mainframe Modernization runtime environmentDelete a runtime environment](delete-environments-m2.md)\.
+ Delete the Amazon S3 buckets you created for this tutorial\. For more information, see [Deleting a bucket](https://docs.aws.amazon.com/AmazonS3/latest/userguide/delete-bucket.html) in the *Amazon S3 User Guide*\.
+ Delete the AWS KMS key and Secrets Manager secret you created for this tutorial\. For more information, see [Deleting AWS KMS keys](https://docs.aws.amazon.com/kms/latest/developerguide/deleting-keys.html) and [Delete a secret](https://docs.aws.amazon.com/secretsmanager/latest/userguide/manage_delete-secret.html)\.
+ Delete the database you created for this tutorial\. For more information, see [Deleting a DB instance](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_GettingStarted.CreatingConnecting.PostgreSQL.html#CHAP_GettingStarted.Deleting.PostgreSQL)\.

## Next steps<a name="tutorial-runtime-mf-next"></a>

As next steps, you can see the following resources:
+ To learn how to set up a build for your modernized applications, see [Tutorial: Setting up the Micro Focus build for the BankDemo sample application](tutorial-build.md)
+ To learn how to set up a CI/CD pipeline for your modernized applications, see [Tutorial: Setting up a CI/CD pipeline for use with Micro Focus Enterprise Developer](tutorial-cicd-mf.md)