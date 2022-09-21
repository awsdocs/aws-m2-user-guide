# Tutorial: Set up AppStream 2\.0 for use with Blu Age Developer IDE<a name="set-up-appstream-ba"></a>

This document describes how to create an AppStream 2\.0 fleet for use with Blu Age Developer IDE\.

**Topics**
+ [Prerequisites](#set-up-aas2-ba-prereqs)
+ [Required artifacts](#set-up-appstream-ba-artifacts)
+ [Create the fleet with AWS CloudFormation](#set-up-appstream-ba-cfn)
+ [Access an instance](#set-up-appstream-ba-access)
+ [Clean up resources](#set-up-appstream-ba-clean)

## Prerequisites<a name="set-up-aas2-ba-prereqs"></a>
+ Download the required artifacts listed in [Required artifacts](#set-up-appstream-ba-artifacts)\.
+ Create an Amazon S3 bucket with the same structure as that shown in [Required artifacts](#set-up-appstream-ba-artifacts)\.

## Required artifacts<a name="set-up-appstream-ba-artifacts"></a>

Use the following links to download the required artifacts:
+ [cfn\-m2\-appstream\-elastic\-fleet\-linux\.yaml](https://d3lkpej5ajcpac.cloudfront.net/appstream/bluage/developer-ide/CloudFormation/cfn-m2-appstream-elastic-fleet-linux.yaml)
+ [cfn\-m2\-appstream\-bluage\-dev\-tools\-linux\.yaml](https://d3lkpej5ajcpac.cloudfront.net/appstream/bluage/developer-ide/CloudFormation/cfn-m2-appstream-bluage-dev-tools-linux.yaml)
+ [cfn\-m2\-appstream\-bluage\-shared\-linux\.yaml](https://d3lkpej5ajcpac.cloudfront.net/appstream/bluage/developer-ide/CloudFormation/cfn-m2-appstream-bluage-shared-linux.yaml)
+ [cfn\-m2\-appstream\-chrome\-linux\.yam](https://d3lkpej5ajcpac.cloudfront.net/appstream/bluage/developer-ide/CloudFormation/cfn-m2-appstream-chrome-linux.yaml)
+ [cfn\-m2\-appstream\-eclipse\-jee\-linux\.yaml](https://d3lkpej5ajcpac.cloudfront.net/appstream/bluage/developer-ide/CloudFormation/cfn-m2-appstream-eclipse-jee-linux.yaml)
+ [cfn\-m2\-appstream\-pgadmin\-linux\.yaml](https://d3lkpej5ajcpac.cloudfront.net/appstream/bluage/developer-ide/CloudFormation/cfn-m2-appstream-pgadmin-linux.yaml)

For this tutorial, all required artifacts must be stored in an Amazon S3 bucket under a common prefix of `appstream/bluage/developer-ide/`\. You will need to recreate this structure in a bucket that you create in order to complete this tutorial\.

The contents of the Amazon S3 bucket are as follows:

![\[The files included in the Amazon S3 bucket for setting up Blu Age developer IDE on AppStream 2.0\]](http://docs.aws.amazon.com/m2/latest/userguide/images/aas-ba-artifacts.png)

The following sections describe how to use those assets\.

Attach the following policy to the Amazon S3 bucket you create for this tutorial\. Make sure to replace `MYBUCKET` with the actual name of the bucket you created\.

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

## Create the fleet with AWS CloudFormation<a name="set-up-appstream-ba-cfn"></a>

The `CloudFormation/` folder contains the templates needed to create and populate the AppStream 2\.0 fleet\.

Those templates expect the above assets to be stored in an `appstream/bluage/developer-ide/` Amazon S3 bucket\. The bucket must be in the same AWS Region as the fleet being created\. If that is not the case, you might need to modify the `S3Bucket` properties accordingly\.

1. Navigate to AWS CloudFormation in the AWS Management console, and choose **Stacks**\.

1. In **Stacks**, choose **Create stack** and **With new Resources \(standard\)**:  
![\[The Stacks page in AWS CloudFormation with Create Stack and With new resources selected.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/cfn-ba-stacks.png)

1. In **Create stack**, choose **Template is ready** and **Upload a template file**:  
![\[The AWS CloudFormation create stack page with template is ready and upload a template file selected.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/cfn-ba-create-stack.png)

1. Choose **Choose file**, and navigate to file `cfn-m2-appstream-elastic-fleet-linux.yaml` to create the fleet\.

Once the fleet is created, repeat the previous steps with the other templates to declare the required applications\.

## Access an instance<a name="set-up-appstream-ba-access"></a>

After you create and start the fleet, you can create a temporary access link for easy access through the native client\.

1. Navigate to AppStream 2\.0 in the AWS Management Console and choose the previously created stack:  
![\[The Stacks page in AppStream 2.0 showing the stack created for AWS Mainframe Modernization.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/aas-ba-stacks.png)

1. In **Stacks**, choose **Action**, then choose **Create Streaming URL**:  
![\[The AppStream 2.0 create streaming URL page.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/aas-ba-create-url.png)

1. In **Create Streaming URL**, enter an \(arbitrary\) User ID and an URL expiration time, then choose **Get URL**\. You will be provided with an URL that you can use to stream to a browser or into the native client \(recommended\)\.

## Clean up resources<a name="set-up-appstream-ba-clean"></a>

The procedure to clean up the created stack and fleets is described in [Create an AppStream 2\.0 Fleet and Stack](https://docs.aws.amazon.com/appstream2/latest/developerguide/set-up-stacks-fleets.html)\.

When the AppStream 2\.0 objects have been deleted, the account administrator can also, if appropriate, clean up the Amazon S3 buckets for Application Settings and Home Folders\.

**Note**  
The home folder for a given user is unique across all fleets, so you might need to retain it if other AppStream 2\.0 stacks are active in the same account\.

Finally, AppStream 2\.0 does not currently allow you to delete users using the console\. Instead, you must use the service API with the CLI\. For more information, see [User Pool Administration](https://docs.aws.amazon.com/appstream2/latest/developerguide/user-pool-admin.html) in the *Amazon AppStream 2\.0 Administration Guide*\.