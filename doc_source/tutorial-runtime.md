# Managed Runtime Tutorial<a name="tutorial-runtime"></a>

This tutorial shows how to deploy and test the BankDemo sample application in the AWS Mainframe Modernization managed runtime with the Micro Focus runtime engine\.

**Topics**
+ [Prerequisites](#tutorial-runtime-prerequisites)
+ [Step 1: Create a runtime environment](#tutorial-runtime-step1)
+ [Step 2: Create an application](#tutorial-runtime-step2)
+ [Step 3: Deploy an application](#tutorial-runtime-step3)
+ [Step 4: Import data sets](#tutorial-runtime-step4)
+ [Step 5: Start an application](#tutorial-runtime-step5)
+ [Step 6: Connect to the BankDemo sample application](#tutorial-runtime-step6)

## Prerequisites<a name="tutorial-runtime-prerequisites"></a>

Before you start the tutorial, make sure you complete the following prerequisites:
+  Create an Amazon Relational Database Service PostgreSQL or Amazon Aurora PostgreSQL DB instance by following the instructions in [Creating a PostgreSQL DB instance and connecting to a database on a PostgreSQL DB instance](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_GettingStarted.CreatingConnecting.PostgreSQL.html) in the *Amazon RDS User Guide*\.
+ Upload the [BankDemo sample application](https://d1vi4vxke6c2hu.cloudfront.net/demo/bankdemo_runtime.zip) to Amazon S3\. For more information, see the [Enterprise Build Tools Tutorial](tutorial-build.md)\.
+ \(Optional\) Create file storage of type Amazon Elastic File System or Amazon FSx for Lustre\.

### Create and configure an AWS Key Management Service key and AWS Secrets Manager secret<a name="tutorial-runtime-prereq-secret"></a>

An AWS KMS key is required in order to create a secret in Secrets Manager\. Follow these steps to securely store the credentials for the Amazon RDS database instance you created for this tutorial\. For more information, see [Prerequisites](#tutorial-runtime-prerequisites)\.

1. To create the key, follow the steps in [Creating keys](https://docs.aws.amazon.com/kms/latest/developerguide/create-keys.html) in the *AWS Key Management Service Developer Guide*\. You will need to edit the key policy for AWS Mainframe Modernization before you finish creating the key\.

1. After you finish selecting the IAM users and roles that can use the key \(step 15\), edit the key policy to grant AWS Mainframe Modernization principal decrypt permissions by adding \(not replacing\) the following policy statements:

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

1. To store the database credentials as a secret in Secrets Manager, follow the steps in [Create a secret](https://docs.aws.amazon.com/secretsmanager/latest/userguide/manage_create-basic-secret.html) in the *AWS Secrets Manager User Guide*\. You will specify the key you created in the previous steps for the encryption key\.

1. After you create the secret, view the details\. In **Resource permissions \- optional**, choose **Edit permissions**\. In the editor, add a resource\-based policy, such as the following, to share the secret with AWS Mainframe Modernization\.

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

1. Choose **Save**\.

### Upload the data sets<a name="tutorial-runtime-prereq-dataset"></a>

Download the [catalog files](https://d1vi4vxke6c2hu.cloudfront.net/demo/catalog.zip) for use with the BankDemo sample application, then upload them to an Amazon S3 bucket, such as `s3://m2-tutorial`\. The following JSON shows all the required data sets\. Replace `$S3_DATASET_PREFIX` with your Amazon S3 bucket that contains the catalog data\. For example, `m2-tutorial/catalog`\.

```
{
    "dataSets": [
        {
            "dataSet": {
                "location": "sql://ESPACDatabase/VSAM/CATALOG.DAT", 
                "name": "CATALOG"
            }, 
            "externalLocation": {
                "s3Location": "$S3_DATASET_PREFIX/CATALOG.DAT"
            }
        }, 
        {
            "dataSet": {
                "location": "sql://ESPACDatabase/VSAM/SPLDSN.dat", 
                "name": "SPLDSN"
            }, 
            "externalLocation": {
                "s3Location": "$S3_DATASET_PREFIX/SPLDSN.dat"
            }
        }, 
        {
            "dataSet": {
                "location": "sql://ESPACDatabase/VSAM/SPLJNO.dat?type=seq\\;reclen=80,80", 
                "name": "SPLJNO"
            }, 
            "externalLocation": {
                "s3Location": "$S3_DATASET_PREFIX/SPLJNO.dat"
            }
        }, 
        {
            "dataSet": {
                "location": "sql://ESPACDatabase/VSAM/SPLJOB.dat", 
                "name": "SPLJOB"
            }, 
            "externalLocation": {
                "s3Location": "$S3_DATASET_PREFIX/SPLJOB.dat"
            }
        }, 
        {
            "dataSet": {
                "location": "sql://ESPACDatabase/VSAM/SPLMSG.dat", 
                "name": "SPLMSG"
            }, 
            "externalLocation": {
                "s3Location": "$S3_DATASET_PREFIX/SPLMSG.dat"
            }
        }, 
        {
            "dataSet": {
                "location": "sql://ESPACDatabase/VSAM/SPLOUT.dat", 
                "name": "SPLOUT"
            }, 
            "externalLocation": {
                "s3Location": "$S3_DATASET_PREFIX/SPLOUT.dat"
            }
        }, 
        {
            "dataSet": {
                "location": "sql://ESPACDatabase/VSAM/SPLSUB.dat", 
                "name": "SPLSUB"
            }, 
            "externalLocation": {
                "s3Location": "$S3_DATASET_PREFIX/SPLSUB.dat"
            }
        }, 
        {
            "dataSet": {
                "location": "sql://ESPACDatabase/VSAM/MFI01V.MFIDEMO.BNKACC.DAT?folder=/DATA", 
                "name": "MFI01V.MFIDEMO.BNKACC"
            }, 
            "externalLocation": {
                "s3Location": "$S3_DATASET_PREFIX/data/MFI01V.MFIDEMO.BNKACC.DAT"
            }
        }, 
        {
            "dataSet": {
                "location": "sql://ESPACDatabase/VSAM/MFI01V.MFIDEMO.BNKATYPE.DAT?folder=/DATA", 
                "name": "MFI01V.MFIDEMO.BNKATYPE"
            }, 
            "externalLocation": {
                "s3Location": "$S3_DATASET_PREFIX/data/MFI01V.MFIDEMO.BNKATYPE.DAT"
            }
        }, 
        {
            "dataSet": {
                "location": "sql://ESPACDatabase/VSAM/MFI01V.MFIDEMO.BNKCUST.DAT?folder=/DATA", 
                "name": "MFI01V.MFIDEMO.BNKCUST"
            }, 
            "externalLocation": {
                "s3Location": "$S3_DATASET_PREFIX/data/MFI01V.MFIDEMO.BNKCUST.DAT"
            }
        }, 
        {
            "dataSet": {
                "location": "sql://ESPACDatabase/VSAM/MFI01V.MFIDEMO.BNKHELP.DAT?folder=/DATA", 
                "name": "MFI01V.MFIDEMO.BNKHELP"
            }, 
            "externalLocation": {
                "s3Location": "$S3_DATASET_PREFIX/data/MFI01V.MFIDEMO.BNKHELP.DAT"
            }
        }, 
        {
            "dataSet": {
                "location": "sql://ESPACDatabase/VSAM/MFI01V.MFIDEMO.BNKTXN.DAT?folder=/DATA", 
                "name": "MFI01V.MFIDEMO.BNKTXN"
            }, 
            "externalLocation": {
                "s3Location": "$S3_DATASET_PREFIX/data/MFI01V.MFIDEMO.BNKTXN.DAT"
            }
        }, 
        {
            "dataSet": {
                "location": "sql://ESPACDatabase/VSAM/YBATTSO.PRC?folder=/PRC\\;type=lseq\\;reclen=80,80", 
                "name": "YBATTSO.PRC"
            }, 
            "externalLocation": {
                "s3Location": "$S3_DATASET_PREFIX/prc/YBATTSO.prc"
            }
        }, 
        {
            "dataSet": {
                "location": "sql://ESPACDatabase/VSAM/YBNKEXTV.PRC?folder=/PRC\\;type=lseq\\;reclen=80,80", 
                "name": "YBNKEXTV.PRC"
            }, 
            "externalLocation": {
                "s3Location": "$S3_DATASET_PREFIX/prc/YBNKEXTV.prc"
            }
        }, 
        {
            "dataSet": {
                "location": "sql://ESPACDatabase/VSAM/YBNKPRT1.PRC?folder=/PRC\\;type=lseq\\;reclen=80,80", 
                "name": "YBNKPRT1.PRC"
            }, 
            "externalLocation": {
                "s3Location": "$S3_DATASET_PREFIX/prc/YBNKPRT1.prc"
            }
        }, 
        {
            "dataSet": {
                "location": "sql://ESPACDatabase/VSAM/YBNKSRT1.PRC?folder=/PRC\\;type=lseq\\;reclen=80,80", 
                "name": "YBNKSRT1.PRC"
            }, 
            "externalLocation": {
                "s3Location": "$S3_DATASET_PREFIX/prc/YBNKSRT1.prc"
            }
        }, 
        {
            "dataSet": {
                "location": "sql://ESPACDatabase/VSAM/KBNKSRT1.TXT?folder=/CTLCARDS\\;type=lseq\\;reclen=80,80", 
                "name": "KBNKSRT1"
            }, 
            "externalLocation": {
                "s3Location": "$S3_DATASET_PREFIX/ctlcards/KBNKSRT1.txt"
            }
        }
    ]
}
```

## Step 1: Create a runtime environment<a name="tutorial-runtime-step1"></a>

1. Open the [AWS Mainframe Modernization console](https://us-west-2.console.aws.amazon.com/m2/home?region=us-west-2#/) and choose **Get started**\.  
![\[Get started creating an environment.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/env-get-started.png)

1. On the **Get started** page, under **What would you like to do?**, choose **Deploy \- Create runtime environment**, and then choose **Continue**\.  
![\[Create a runtime environment.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/env-deploy.png)

1. On the **Create environment** page, under **Permissions \(one time setup\)**, choose **I grant AWS Mainframe Modernization the required permissions\.** These permissions allow AWS Mainframe Modernization to create AWS resources on your behalf\. This is a one time setup step, which will grant AWS Mainframe Modernization the necessary permissions\. If permissions were previously granted, choose **Continue**\.  
![\[Grant or confirm permissions.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/env-perm.png)

1. Specify a name and optional description for the runtime environment, and choose **Next**\.  
![\[Specify basic information.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/env-step1.png)

1. Under **Availability**, choose **High availability cluster**\. Under **Resources**, choose an instance type and the number of instances you want\. Then choose **Next**\.  
![\[Specify configurations.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/env-config.png)

1. On the **Attach storage** page, optionally specify either Amazon EFS or Amazon FSx file systems\. Then choose **Next**\.  
![\[Attach storage (optional).\]](http://docs.aws.amazon.com/m2/latest/userguide/images/env-storage.png)

1. On the **Review and create** page, review all the configurations you selected for the runtime environment and choose **Create Environment**\.  
![\[Review configurations.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/env-review.png)

1. Wait until environment creation succeeds and the status changes to **Available**\. It might take up to two minutes\.  
![\[Environment created successfully.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/env-confirm.png)

## Step 2: Create an application<a name="tutorial-runtime-step2"></a>

1. In the navigation pane, choose **Applications**\. Then choose **Create application**\.  
![\[Create application.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/app-create.png)

1. On the **Specify basic information** page, specify the application name and an optional description\. You can also provide optional tagging information\. Then choose **Next**\.  
![\[Specify basic information.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/app-step1.png)

1. On the **Specify resources and configurations** page, choose whether you want to specify the application definition using the inline editor or provide an Amazon S3 bucket where the application definition file is stored\. Either type the application definition or provide the Amazon S3 location\. Then choose **Next**\.  
![\[Specify resources and configurations.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/app-config.png)

   You can use the following sample application definition file\. Make sure to replace `$SECRET_ARN`, `$S3_BUCKET`, and `$S3_PREFIX` with the correct values for you\.

   ```
   {
       "resources": [
           {
               "resource-type": "vsam-config",
               "resource-id": "vsam-1",
               "properties": {
                   "secret-manager-arn": "$SECRET_ARN"
               }
           },
           {
               "resource-type": "cics-resource-definition",
               "resource-id": "resource-definition-1", 
               "properties": {
                   "file-location": "${s3-source}/RDEF",
                   "system-initialization-table": "BNKCICV"
               }
           },
           {
               "resource-type": "cics-transaction",
               "resource-id": "transaction-1", 
               "properties": {
                   "file-location": "${s3-source}/transaction"
               }
           },
           {
               "resource-type": "mf-listener",
               "resource-id": "listener-1", 
               "properties": {
                   "port": 6000,
                   "conversation-type": "tn3270"
               }
           },
           {
               "resource-type": "xa-resource",
               "resource-id": "xa-resource-1",
               "properties": {
                   "name": "XASQL",
                   "module": "${s3-source}/xa/ESPGSQLXA64.so",
                   "secret-manager-arn": "$SECRET_ARN"
               }
           },
           {
               "resource-type": "jes-initiator",
               "resource-id": "jes-initiator-1",
               "properties": {
                   "initiator-class": "A",
                   "description": "initiator...."
               }
           
           },
           {
               "resource-type": "jcl-job",
               "resource-id": "jcl-job-1",
               "properties": {
                   "file-location": "${s3-source}/jcl"
               }
           }
       ],
       "source-locations": [
           {
               "source-id": "s3-source",
               "source-type": "s3",
               "properties": {
                   "s3-bucket": "$S3_BUCKET",
                   "s3-key-prefix": "$S3_PREFIX"
               }
           }
       ]
   }
   ```
**Note**  
This file is subject to change\.

1. On the **Review and create** page, review the information you provided and choose **Create application**\.  
![\[Review and create.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/app-review.png)

1. Wait until the application creation operation succeeds and the status changes to **Available**\.  
![\[Application created successfully.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/app-confirm.png)

## Step 3: Deploy an application<a name="tutorial-runtime-step3"></a>

1. In the navigation pane, choose **Applications**, then choose **BankDemo**\. Choose **Actions**, then choose **Deploy application**\.  
![\[Deploy the application.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/deploy-action.png)

1. Choose the **Tutorial** environment and then choose **Deploy**\.  
![\[Choose the target environment.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/deploy-env.png)

1. Wait until the BankDemo application deployment succeeds and the status changes to **Ready**\.  
![\[Deployment succeeded.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/deploy-confirm.png)

## Step 4: Import data sets<a name="tutorial-runtime-step4"></a>

1. In the navigation pane, choose **Applications**, then choose **BankDemo**\. Choose the **Data sets** tab\. Then choose **Import**\.  
![\[Select the data set tab.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/import-start.png)

1. Choose **Add new item** to add each data set and choose **Submit** after you provide the information for each data set\.   
![\[Choose the data sets to import.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/import-submit.png)

   The following table lists the data sets that you need to import\. Replace `$S3_DATASET_PREFIX` with your Amazon S3 bucket that contains the catalog data\. For example, `S3_DATASET_PREFIX=s3://m2-tutorial/catalog`\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/m2/latest/userguide/tutorial-runtime.html)

1. Wait until the data set import process completes and the status changes to **Completed**\.  
![\[Import succeeded.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/import-confirm.png)

## Step 5: Start an application<a name="tutorial-runtime-step5"></a>

1. In the navigation pane, choose **Applications**, then choose **BankDemo**\. Choose **Actions**, then choose **Start application**\.  
![\[Start the application.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/start-action.png)

1. Wait until the BankDemo application starts successfully and the status changes to **Running**\.  
![\[Application start succeeded.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/start-confirm.png)

## Step 6: Connect to the BankDemo sample application<a name="tutorial-runtime-step6"></a>

1. Start the terminal emulator you want\. This tutorial uses Micro Focus Rumba\+\.  
![\[Start the terminal emulator.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/connect-start.png)

1. Choose **Connection**, then **Configure**, then **TN3270**\.  
![\[Configure the terminal emulator step 1.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/connect-config-1.png)  
![\[Configure the terminal emulator step 2.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/connect-config-2.png)

1. Enter the BANK transaction name\.  
![\[Specify the name of the application.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/connect-name.png)

1. Type `B0001` for the username and `A` for the password\.  
![\[Login to the application.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/connect-login.png)

1. After you log in successfully, you can navigate through the BankDemo application\.  
![\[Navigate through the application part 1.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/connect-nav-1.png)  
![\[Navigate through the application part 2.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/connect-nav-2.png)