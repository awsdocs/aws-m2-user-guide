# Tutorial: Setting up the build for the BankDemo sample application<a name="tutorial-build"></a>

This tutorial demonstrates how to use AWS CodeBuild to compile the BankDemo sample application source code from Amazon S3 and then export the compiled code back to Amazon S3\.

AWS CodeBuild is a fully managed continuous integration service that compiles source code, runs tests, and produces software packages that are ready to deploy\. With CodeBuild, you can use prepackaged build environments, or you can create custom build environments that use your own build tools\. This demo scenario uses the second option\. It consists of a CodeBuild build environment using a pre\-packaged Docker image\.

**Important**  
Before you start your mainframe modernization project, we recommend that you learn about the [AWS Migration Acceleration Program \(MAP\) for Mainframe](https://aws.amazon.com/migration-acceleration-program/mainframe/) or contact [AWS mainframe specialists](mailto: mainframe@amazon.com) to learn about the required steps for modernizing a mainframe application\.

**Topics**
+ [Prerequisites](#tutorial-build-prerequisites)
+ [Step 1: Create Amazon S3 buckets](#tutorial-build-step1)
+ [Step 2: Create the build spec file](#tutorial-build-step2)
+ [Step 3: Upload the source files](#tutorial-build-step3)
+ [Step 4: Create IAM policies](#tutorial-build-step4)
+ [Step 5: Create an IAM role](#tutorial-build-step5)
+ [Step 6: Attach the IAM policies to the IAM role](#tutorial-build-step6)
+ [Step 7: Create the CodeBuild project](#tutorial-build-create-build-project)
+ [Step 8: Start the build](#tutorial-build-step8)
+ [Step 9: Download output artifacts](#tutorial-build-step9)
+ [Clean up resources](#tutorial-build-clean)

## Prerequisites<a name="tutorial-build-prerequisites"></a>

Before you start this tutorial, complete the following prerequisites\.
+ Download the [BankDemo sample application](https://d3lkpej5ajcpac.cloudfront.net/demo/mf/BANKDEMO-build.zip)\.
+ In the left menu, click on **Tools**.Locate MicroFocus Analysis, development, and build assets and click on **Share Assets with my AWS Account**

## Step 1: Create Amazon S3 buckets<a name="tutorial-build-step1"></a>

In this step, you create two Amazon S3 buckets to hold the source code \(the input bucket\) and the build output \(the output bucket\)\. Follow the instructions in [Creating, configuring, and working with Amazon S3 buckets](https://docs.aws.amazon.com/AmazonS3/latest/userguide/creating-buckets-s3.html) in the *Amazon S3 User Guide*\.

1. Log in to the Amazon S3 console and choose **Create bucket**\.

1. In **General configuration**, provide a name for the bucket and specify the AWS Region where you want the bucket created\. An example name is `codebuild-regionId-accountId-input-bucket`, where `regionId` is the AWS Region of the bucket and `accountId` is your AWS account ID\.
**Note**  
If you are creating the bucket in a different AWS Region from US East \(N\. Virginia\), specify the `LocationConstraint` parameter\. For more information, see [Create Bucket](https://docs.aws.amazon.com/AmazonS3/latest/API/API_CreateBucket.html) in the *Amazon Simple Storage Service API Reference*\.

1. Retain all other settings and choose **Create bucket\.**

1. Repeat steps 1\-3 to create the output bucket\. An example name is `codebuild-regionId-accountId-output-bucket`, where `regionId` is the AWS Region of the bucket and `accountId` is your AWS account ID\.

## Step 2: Create the build spec file<a name="tutorial-build-step2"></a>

In this step, you create a build spec file, which provides build commands and related settings, in YAML format, to CodeBuild for running the build\. For more information, see [Build specification reference for CodeBuild](https://docs.aws.amazon.com/codebuild/latest/userguide/build-spec-ref.html) in the *AWS CodeBuild User Guide*\.

1. Create a file named `buildspec.yml` at the local root directory of your source code folder\.

1. Add the following content to the end of the file:

   ```
   version: 0.2
   env:
     exported-variables:
       - CODEBUILD_BUILD_ID
       - CODEBUILD_BUILD_ARN
   phases:
     install:
       runtime-versions:
         python: 3.7
     pre_build:
       commands:
         - echo Installing source dependencies...
         - ls -lR $CODEBUILD_SRC_DIR/source
     build:
       commands:
         - echo Build started on `date`
         - /start-build.sh -Dbasedir=$CODEBUILD_SRC_DIR/source -Dloaddir=$CODEBUILD_SRC_DIR/target 
     post_build:
       commands:
         - ls -lR $CODEBUILD_SRC_DIR/target
         - echo Build completed on `date`
   artifacts:
     files:
       - $CODEBUILD_SRC_DIR/target/**
   ```

   where `CODEBUILD_BUILD_ID`, `CODEBUILD_BUILD_ARN`, `$CODEBUILD_SRC_DIR/source`, and `$CODEBUILD_SRC_DIR/target` are environment variables you can pass to CodeBuild\. For more information, see [Environment variables in build environments](https://docs.aws.amazon.com/codebuild/latest/userguide/build-env-ref-env-vars.html)\.

## Step 3: Upload the source files<a name="tutorial-build-step3"></a>

In this step, you upload the BankDemo sample application source code to the input bucket you created in Amazon S3\.

1. Log in to the Amazon S3 console and choose **Buckets** in the left navigation pane\. Then choose the input bucket you created previously\.

1. In **Objects**, choose **Upload**\.

1. In **Files and folders**, choose **Add Folder**\.

1. Navigate to the location where you downloaded the BankDemo sample and choose the BankDemo folder\.

1. Choose **Upload**\.

## Step 4: Create IAM policies<a name="tutorial-build-step4"></a>

In this step, you create two [IAM policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies.html)\. One policy grants permissions to access and use the Docker image that contains the Micro Focus build tools\. This policy is not customized for customers\. The other policy grants permissions to interact with the input and output buckets, as well as with the [Amazon CloudWatch logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/WhatIsCloudWatchLogs.html) that CodeBuild generates\. 

**To create a policy for accessing Docker images**

1. Log in to the IAM console\.

1. Choose **Policies** in the left navigation pane\. Then choose **Create policy**\.

1. Choose the **JSON** tab\.

1. Copy the following policy document and paste it into the JSON editor\.

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Effect": "Allow",
               "Action": [
                   "ecr:GetAuthorizationToken"
               ],
               "Resource": "*"
           },
           {
               "Effect": "Allow",
               "Action": [
                   "ecr:BatchCheckLayerAvailability",
                   "ecr:GetDownloadUrlForLayer",
                   "ecr:BatchGetImage"
               ],
               "Resource": "arn:aws:ecr:*:673918848628:repository/m2-enterprise-build-tools"
           },
           {
               "Effect": "Allow",
               "Action": [
                   "s3:PutObject"
               ],
               "Resource": "arn:aws:s3:::aws-m2-repo-*/*"
           }
       ]
   }
   ```

1. Choose **Next: Tags**\.

1. Choose **Next: Review**\.

1. In **Name**, provide a name for the policy; for example, `m2CodeBuildPolicy`\.

1. Choose **Create policy**\.

**To create a policy for interacting with buckets and logs**

1. Log in to the IAM console\.

1. Choose **Policies** in the left navigation pane\. Then choose **Create policy**\.

1. Choose the **JSON** tab\.

1. Copy the following policy document and paste it into the JSON editor\. Make sure to update `regionId` to the AWS Region and `accountId` to your AWS account\.

   ```
   {
         "Version": "2012-10-17",
         "Statement": [
             {
                 "Action": [
                     "logs:CreateLogGroup",
                     "logs:CreateLogStream",
                     "logs:PutLogEvents"
                 ],
                 "Resource": [
                     "arn:aws:logs:regionId:accountId:log-group:/aws/codebuild/codebuild-bankdemo-project",
                     "arn:aws:logs:regionId:accountId:log-group:/aws/codebuild/codebuild-bankdemo-project:*"
                 ],
                 "Effect": "Allow"
             },
             {
                 "Action": [
                     "s3:PutObject",
                     "s3:GetObject",
                     "s3:GetObjectVersion",
                     "s3:GetBucketAcl",
                     "s3:GetBucketLocation",
                     "s3:List*"
                 ],
                 "Resource": [
                     "arn:aws:s3:::codebuild-regionId-accountId-input-bucket",
                     "arn:aws:s3:::codebuild-regionId-accountId-input-bucket/*",
                     "arn:aws:s3:::codebuild-regionId-accountId-output-bucket",
                     "arn:aws:s3:::codebuild-regionId-accountId-output-bucket/*"
                 ],
                 "Effect": "Allow"
             }
         ]
     }
   ```

1. Choose **Next: Tags\.**

1. Choose **Next: Review**\.

1. In **Name**, provide a name for the policy; for example, `BankdemoCodeBuildRolePolicy`\.

1. Choose **Create policy**\.

## Step 5: Create an IAM role<a name="tutorial-build-step5"></a>

In this step, you create a new [IAM role](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html) that will grant CodeBuild permissions to interact with AWS resources on your behalf, after you associate the IAM policies that you previously created\.

1. Log into the IAM console and choose **Roles** in the left navigation pane\.

1. Choose **Create role**\.

1. In **Trusted entity type**, choose **AWS service**\.

1. In **Use cases for other AWS services**, choose **CodeBuild**, then choose **CodeBuild** again\.

1. Choose **Next**\.

1. In **Add permissions**, choose **Next**\. You will assign a policy to the role later\.

1. In **Role details**, provide a name; for example, `BankdemoCodeBuildServiceRole`\.

1. In **Select trusted entities**, verify that the policy document looks like the following:

   ```
   {
             "Version": "2012-10-17",
             "Statement": [
               {
                 "Effect": "Allow",
                 "Principal": {
                   "Service": "codebuild.amazonaws.com"
                 },
                 "Action": "sts:AssumeRole"
               }
             ]
           }
   ```

1. Choose **Create role**\.

## Step 6: Attach the IAM policies to the IAM role<a name="tutorial-build-step6"></a>

In this step, you attach the two IAM policies you previously created to the `BankdemoCodeBuildServiceRole` IAM role\.

1. Log into the IAM console and choose **Roles** in the left navigation pane\.

1. In **Roles**, choose the role you created previously; for example, `BankdemoCodeBuildServiceRole`\.

1. In **Permissions policies**, choose **Add permissions**, then **Attach policies**\.

1. In **Other permissions policies**, choose the policies you created previously; for example, `m2CodeBuildPolicy` and `BankdemoCodeBuildRolePolicy`\.

1. Choose **Attach policies\.**

## Step 7: Create the CodeBuild project<a name="tutorial-build-create-build-project"></a>

In this step, you create the CodeBuild project\.

1. Log into the CodeBuild console and choose **Create build project**\.

1. In **Project configuration**, provide a name; for example, `codebuild-bankdemo-project`\.

1. In **Source**, for **Source provider**, choose **Amazon S3** then choose the input bucket you created previously; for example, `codebuild-regionId-accountId-input-bucket`\.

1. In **S3 object key or S3 folder**, enter the name of the folder in the Amazon S3 bucket where you uploaded the source code; for example, `bankdemo`\.

1. In **Environment**, choose **Custom image**\.

1. In **Environment type**, choose **Linux**\.

1. In **Image registry**, choose **Other registry**\.

1. In **External registry URL**, enter `673918848628.dkr.ecr.us-west-2.amazonaws.com/m2-enterprise-build-tools:latest` 

1. In **Service role**, choose **Existing service role**, and in **Role ARN**, choose the service role you created previously; for example, `BankdemoCodeBuildServiceRole`\.

1. In **Buildspec**, choose **Use a buildspec file**\.

1. In **Artifacts**, for **Type**, choose **Amazon S3**, and choose the output bucket you created previously; for example, `codebuild-regionId-accountId-output-bucket`\.

1. In **Name**, enter the name of a folder in the bucket that you want to contain the build output artifacts; for example, `bankdemo-output.zip`\.

1. In **Artifacts packaging**, choose **Zip**\.

1. Choose **Create build project**\.

## Step 8: Start the build<a name="tutorial-build-step8"></a>

In this step, you start the build\.

1. Log into the CodeBuild console\.

1. In the left navigation pane, choose **Build projects**\.

1. Choose the build project you created previously; for example, `codebuild-bankdemo-project`\.

1. Choose **Start build**\.

This command will trigger the build, which will run asynchronously\. The output of the command is a JSON that includes the attribute id, which is a reference to the CodeBuild build id of the build that you just started\. You can view the status of the build in the CodeBuild console\. You can also see detailed logs about the build execution\. For more information, see [View detailed build information](https://docs.aws.amazon.com/codebuild/latest/userguide/getting-started-build-log-console.html) in the *AWS CodeBuild User Guide*\.

When the current phase is COMPLETED, it means that your build finished successfully, and your compiled artifacts are ready on Amazon S3\.

## Step 9: Download output artifacts<a name="tutorial-build-step9"></a>

In this step, you download the output artifacts from Amazon S3\.

1. Log in to the Amazon S3 console\.

1. In **Buckets**, choose the name of the output bucket you created previously; for example, `codebuild-regionId-accountId-output-bucket`\.

1. Choose `Download`\.

## Clean up resources<a name="tutorial-build-clean"></a>

If you no longer need the resources you created for this tutorial, delete them so that you won't continue to be charged for them\. Complete the following steps:
+ Delete the Amazon S3 buckets you created for this tutorial\. For more information, see [Deleting a bucket](https://docs.aws.amazon.com/AmazonS3/latest/userguide/delete-bucket.html) in the *Amazon Simple Storage Service User Guide*\.
+ Delete the IAM policies you created for this tutorial\. For more information, see [Deleting IAM policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_manage-delete.html) in the *IAM User Guide*\.
+ Delete the IAM role you created for this tutorial\. For more information, see [Deleting roles or instance profiles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_manage_delete.html) in the *IAM User Guide*\.
+ Delete the CodeBuild project you created for this tutorial\. For more information, see [Delete a build project in CodeBuild](https://docs.aws.amazon.com/codebuild/latest/userguide/delete-project.html) in the *AWS CodeBuild User Guide*\.
