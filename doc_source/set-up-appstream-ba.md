# Tutorial: Set up AppStream 2\.0 for Blu Age Developer IDE<a name="set-up-appstream-ba"></a>

This document describes how to set up Blu Age Developer IDE on an AppStream 2\.0 fleet\.

**Topics**
+ [Prerequisite](#set-up-aas2-ba-prereqs)
+ [Step 1: Create an Amazon S3 bucket](#set-up-aas2-ba-create-bucket)
+ [Step 2: Attach a policy to the S3 bucket](#set-up-aas2-ba-create-bucket-policy)
+ [Step 3: Upload files to the Amazon S3 bucket](#set-up-aas2-ba-upload)
+ [Step 4: Download AWS CloudFormation templates](#set-up-aas2-ba-download-templates)
+ [Step 5: Create the fleet with AWS CloudFormation](#set-up-appstream-ba-cfn)
+ [Step 6: Access an instance](#set-up-appstream-ba-access)
+ [Clean up resources](#set-up-appstream-ba-clean)

## Prerequisite<a name="set-up-aas2-ba-prereqs"></a>

Download the [archive file](https://d3lkpej5ajcpac.cloudfront.net/appstream/bluage/appstream-bluage-developer-ide.zip) that contains the artifacts that you need to set up Blu Age Developer IDE under AppStream 2\.0\.

**Note**  
This is a large file\. If you have problems with the operation timing out, we recommend using an Amazon EC2 instance to improve the upload and download performance\.

## Step 1: Create an Amazon S3 bucket<a name="set-up-aas2-ba-create-bucket"></a>

Create an Amazon S3 bucket in the same AWS Region as the AppStream 2\.0 fleet that you will create\. This bucket will contain the artifacts that you need to complete this tutorial\.

## Step 2: Attach a policy to the S3 bucket<a name="set-up-aas2-ba-create-bucket-policy"></a>

Attach the following policy to the bucket that you create for this tutorial\. Make sure to replace `MYBUCKET` with the actual name of the bucket that you create\.

```
{
    "Version": "2012-10-17",
    "Statement": [{
        "Sid": "AllowAppStream2.0ToRetrieveObjects",
        "Effect": "Allow",
        "Principal": {
            "Service": "appstream.amazonaws.com"
        },
        "Action": "s3:GetObject",
        "Resource": "arn:aws:s3:::MYBUCKET/*"
    }]
}
```

## Step 3: Upload files to the Amazon S3 bucket<a name="set-up-aas2-ba-upload"></a>

Unzip the files you downloaded in the Prerequisite and upload the `appstream` folder to your bucket\. Uploading this folder creates the correct structure in your bucket\. For more information, see [Uploading objects](https://docs.aws.amazon.com/AmazonS3/latest/userguide/upload-objects.html) in the *Amazon S3 User Guide*\.

## Step 4: Download AWS CloudFormation templates<a name="set-up-aas2-ba-download-templates"></a>

Download the following AWS CloudFormation templates\. You need these templates to create and populate the AppStream 2\.0 fleet\.
+ [cfn\-m2\-appstream\-elastic\-fleet\-linux\.yaml](https://d3lkpej5ajcpac.cloudfront.net/appstream/bluage/developer-ide/CloudFormation/cfn-m2-appstream-elastic-fleet-linux.yaml)
+ [cfn\-m2\-appstream\-bluage\-dev\-tools\-linux\.yaml](https://d3lkpej5ajcpac.cloudfront.net/appstream/bluage/developer-ide/CloudFormation/cfn-m2-appstream-bluage-dev-tools-linux.yaml)
+ [cfn\-m2\-appstream\-bluage\-shared\-linux\.yaml](https://d3lkpej5ajcpac.cloudfront.net/appstream/bluage/developer-ide/CloudFormation/cfn-m2-appstream-bluage-shared-linux.yaml)
+ [cfn\-m2\-appstream\-chrome\-linux\.yaml](https://d3lkpej5ajcpac.cloudfront.net/appstream/bluage/developer-ide/CloudFormation/cfn-m2-appstream-chrome-linux.yaml)
+ [cfn\-m2\-appstream\-eclipse\-jee\-linux\.yaml](https://d3lkpej5ajcpac.cloudfront.net/appstream/bluage/developer-ide/CloudFormation/cfn-m2-appstream-eclipse-jee-linux.yaml)
+ [cfn\-m2\-appstream\-pgadmin\-linux\.yaml](https://d3lkpej5ajcpac.cloudfront.net/appstream/bluage/developer-ide/CloudFormation/cfn-m2-appstream-pgadmin-linux.yaml)

## Step 5: Create the fleet with AWS CloudFormation<a name="set-up-appstream-ba-cfn"></a>

In this step, you use the `cfn-m2-appstream-elastic-fleet-linux.yaml` AWS CloudFormation template to create an AppStream 2\.0 fleet and stack to host the Blu Age Developer IDE\. After you create the fleet and stack, you will run the other AWS CloudFormation templates you downloaded in the previous step to install the Developer IDE and other required tools\.

1. Navigate to AWS CloudFormation in the AWS Management console, and choose **Stacks**\.

1. In **Stacks**, choose **Create stack** and **With new Resources \(standard\)**:  
![\[The Stacks page in AWS CloudFormation with Create Stack and With new resources selected.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/cfn-ba-stacks.png)

1. In **Create stack**, choose **Template is ready** and **Upload a template file**:  
![\[The AWS CloudFormation create stack page with template is ready and upload a template file selected.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/cfn-ba-create-stack.png)

1. Choose **Choose file**, and navigate to file `cfn-m2-appstream-elastic-fleet-linux.yaml`\. Choose **Next**\.

1. In **Specify stack details**, provide the following information:
   + A name for the stack\.
   + Your default security group and two subnets of that security group\.

1. Choose **Next**, and then choose **Next** again\.

1. Choose **I acknowledge that AWS CloudFormation might create IAM resources with custom names\.**, and then choose **Submit**\.

1. After you create the fleet, create CloudFormation stacks with the other downloaded templates to finish setting up the applications\. Make sure to update **BucketName** each time to point to the correct S3 bucket\. You can edit the **BucketName** in the CloudFormation console\. Alternatively, you can edit the template files directly and update the `S3Bucket` property\.

**Note**  
The downloaded templates expect to find assets in an S3 bucket with a folder structure called `appstream/bluage/developer-ide/`\. The bucket must be in the same AWS Region as the fleet that you created\.

## Step 6: Access an instance<a name="set-up-appstream-ba-access"></a>

After you create and start the fleet, you can create a temporary link to access the fleet through the native client\.

1. Navigate to AppStream 2\.0 in the AWS Management Console and choose the previously created stack:  
![\[The Stacks page in AppStream 2.0 showing the stack created for AWS Mainframe Modernization.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/aas-ba-stacks.png)

1. On the stack details page, choose **Action**, then choose **Create Streaming URL**:  
![\[The AppStream 2.0 create streaming URL page.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/aas-ba-create-url.png)

1. In **Create Streaming URL**, enter an arbitrary User ID and a URL expiration time, and then choose **Get URL**\. You get an URL that you can use to stream to a browser or into the native client\. We recommend that you stream into the native client\.

## Clean up resources<a name="set-up-appstream-ba-clean"></a>

For the procedure to clean up the created stack and fleets, see [Create an AppStream 2\.0 Fleet and Stack](https://docs.aws.amazon.com/appstream2/latest/developerguide/set-up-stacks-fleets.html)\.

When you've deleted the AppStream 2\.0 objects, you or the account administrator can also clean up the S3 buckets for Application Settings and Home Folders\.

**Note**  
The home folder for a given user is unique across all fleets, so you might need to retain it if other AppStream 2\.0 stacks are active in the same account\.

You can't use the AppStream 2\.0 console to delete users\. Instead, you must use the service API with the AWS CLI\. For more information, see [User Pool Administration](https://docs.aws.amazon.com/appstream2/latest/developerguide/user-pool-admin.html) in the *Amazon AppStream 2\.0 Administration Guide*\.