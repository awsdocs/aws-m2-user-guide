# Tutorial: Set up AppStream 2\.0 for use with Micro Focus Enterprise Analyzer and Micro Focus Enterprise Developer<a name="set-up-appstream"></a>

This document is intended for members of the customer operations team\. It describes how to set up Amazon AppStream 2\.0 fleets and stacks to host the Micro Focus Enterprise Analyzer and Micro Focus Enterprise Developer tools used with AWS Mainframe Modernization\. Micro Focus Enterprise Analyzer is usually used during the Assess phase and Micro Focus Enterprise Developer is usually used during the Migrate and Modernize phase of the AWS Mainframe Modernization approach\. If you plan to use both Enterprise Analyzer and Enterprise Developer you must create separate fleets and stacks for each tool\. Each tool requires its own fleet and stack because their licensing terms are different\.

**Important**  
The steps in this tutorial are based on the downloadable AWS CloudFormation template [cfn\-m2\-appstream\-fleet\-ea\-ed\.yaml](https://d1vi4vxke6c2hu.cloudfront.net/tutorial/cfn-m2-appstream-fleet-ea-ed.yaml)\.  

**Topics**
+ [Prerequisites](#tutorial-aas-prerequisites)
+ [Step 1: Get the AppStream 2\.0 images](#tutorial-aas-step1)
+ [Step 2: Create the stack using the AWS CloudFormation template](#tutorial-aas-step2)
+ [Step 3: Create a user in AppStream 2\.0](#tutorial-aas-step3)
+ [Step 4: Log in to AppStream 2\.0](#tutorial-aas-step4)
+ [Step 5: Verify buckets in Amazon S3 \(optional\)](#tutorial-aas-step5)
+ [Next steps](#tutorial-aas-next-steps)
+ [Clean up resources](#tutorial-aas-cleanup)

## Prerequisites<a name="tutorial-aas-prerequisites"></a>
+ Download the template: [cfn\-m2\-appstream\-fleet\-ea\-ed\.yaml](https://d1vi4vxke6c2hu.cloudfront.net/tutorial/cfn-m2-appstream-fleet-ea-ed.yaml)\.
+ Get the ID of your default VPC and security group\. For more information on the default VPC, see [Default VPCs](https://docs.aws.amazon.com/vpc/latest/userguide/default-vpc.html) in the *Amazon VPC User Guide*\. For more information on the default security group, see [Default and custom security groups](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/default-custom-security-groups.html) in the *Amazon EC2 User Guide for Linux Instances*\. 
+ Make sure your IAM user has the following permissions:
  + create stacks, fleets, and users in AppStream 2\.0\.
  + create stacks in AWS CloudFormation using a template\.
  + create buckets and upload files to buckets in Amazon S3\.
  + download credentials \(`access_key_id` and `secret_access_key`\) from IAM\.

## Step 1: Get the AppStream 2\.0 images<a name="tutorial-aas-step1"></a>

In this step, you share the AppStream 2\.0 images for Enterprise Analyzer and Enterprise Developer with your AWS account\.

1. Open the AWS Mainframe Modernization console at [https://console\.aws\.amazon\.com/m2/](https://us-west-2.console.aws.amazon.com/m2/home?region=us-west-2#/)\.

1. In the left navigation, choose **Tools**\.

1. In **Analysis, development, and build assets**, choose **Share assets with my AWS account**\.  
![\[The AWS Mainframe Modernization tools page showing share assets with my AWS account.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/2022-08-17-m2-tools-mf-images.png)

## Step 2: Create the stack using the AWS CloudFormation template<a name="tutorial-aas-step2"></a>

In this step, you use the downloaded AWS CloudFormation template to create an AppStream 2\.0 stack and fleet for running Micro Focus Enterprise Analyzer\. You can repeat this step later to create another AppStream 2\.0 stack and fleet for running Micro Focus Enterprise Developer, since each tool requires its own fleet and stack in AppStream 2\.0\. For more information on AWS CloudFormation stacks, see [Working with stacks](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/stacks.html) in the *AWS CloudFormation User Guide*\.

**Note**  
AWS Mainframe Modernization adds an additional fee to the standard AppStream 2\.0 pricing for the use of Enterprise Analyzer and Enterprise Developer\. For more information, see [AWS Mainframe Modernization Pricing](http://aws.amazon.com/mainframe-modernization/pricing/)\.

1. Download the [cfn\-m2\-appstream\-fleet\-ea\-ed\.yaml](https://d1vi4vxke6c2hu.cloudfront.net/tutorial/cfn-m2-appstream-fleet-ea-ed.yaml) template, if necessary\.

1. Open the AWS CloudFormation console and choose **Create Stack**\.

1. In **Prerequisite \- Prepare template**, choose **Template is ready**\.

1. In **Specify Template**, choose **Upload a template file**\.

1. In **Upload a template file**, choose **Choose file** and upload the [cfn\-m2\-appstream\-fleet\-ea\-ed\.yaml](https://d1vi4vxke6c2hu.cloudfront.net/tutorial/cfn-m2-appstream-fleet-ea-ed.yaml) template\.

1. Choose **Next**\.  
![\[The AWS CloudFormation create stack page showing the cfn-m2-appstream-fleet-ea-ed.yaml template selected.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/cfn-create-stack.png)

1. On **Specify stack details**, enter the following information:
   + In **Stack name**, enter a name of your choice\. For example, **m2\-ea**\.
   + In **AppStreamApplication**, choose **ea**\.
   + In **AppStreamFleetSecurityGroup**, choose your default VPC’s default security group\.
   + In **AppStreamFleetVpcSubnet**, choose a subnet within your default VPC\.
   + In **AppStreamImageName**, choose the image starting with `m2-enterprise-analyzer`\.
   + Accept the defaults for the other fields, then choose **Next**\.  
![\[The AWS CloudFormation specify stack details page with Enterprise Analyzer options filled in.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/cfn-specify-stack-details.png)

1. Accept all defaults, then choose **Next** again\.

1. On **Review**, make sure all the parameters are what you intend\.

1. Scroll to the bottom, choose **I acknowledge that AWS CloudFormation might create IAM resources with custom names**, and choose **Create Stack**\.

It takes between 20 and 30 minutes for the stack and fleet to be created\. You can choose **Refresh** to see the AWS CloudFormation events as they occur\.  

## Step 3: Create a user in AppStream 2\.0<a name="tutorial-aas-step3"></a>

While you are waiting for AWS CloudFormation to finish creating the stack, you can create one or more users in AppStream 2\.0\. These users are those who will be using Enterprise Analyzer in AppStream 2\.0\. You will need to specify an email address for each user, and each user needs to correspond to an IAM user with sufficient permissions to create buckets in Amazon S3, upload files to a bucket, and link to a bucket to map its contents\.

1. Open the AppStream 2\.0 console\.

1. In the left navigation, choose **User pool**\.

1. Choose **Create user**\.

1. Provide an email address where the user can receive an email invitation to use AppStream 2\.0, a first name and last name, and choose **Create user**\.

1. Repeat if necessary to create more users\. The email address for each user must be unique\.

For more information on creating AppStream 2\.0 users, see [AppStream 2\.0 User Pools](https://docs.aws.amazon.com/appstream2/latest/developerguide/user-pool.html) in the *Amazon AppStream 2\.0 Administration Guide*\.

When AWS CloudFormation finishes creating the stack, you can assign the user you created to the stack, as follows:

1. Open the AppStream 2\.0 console\.

1. Choose the user name\.

1. Choose **Action**, then **Assign stack**\.

1. In **Assign stack**, choose the stack that begins with `m2-appstream-stack-ea`\.

1. Choose **Assign stack**\.  
![\[The AppStream 2.0 assign stack page showing a user and the Enterprise Analyzer stack to be assigned.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/aas-assign-stack.png)

Assigning a user to a stack causes AppStream 2\.0 to send an email to the user at the address you provided\. This email contains a link to the AppStream 2\.0 login page\.

## Step 4: Log in to AppStream 2\.0<a name="tutorial-aas-step4"></a>

In this step, you log in to AppStream 2\.0 using the link in the email sent by AppStream 2\.0 to the user you created in [Step 3: Create a user in AppStream 2\.0](#tutorial-aas-step3)\.

1. Log in to AppStream 2\.0 using the link provided in the email sent by AppStream 2\.0\.

1. Change your password, if prompted\. The AppStream 2\.0 screen that you see is similar to the following:  
![\[A sample AppStream 2.0 login screen showing the desktop icon.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/aas-login-screen.png)

1. Choose **Desktop**\.

1. On the task bar, choose **Search** and enter **D:** to navigate to the Home Folder\.
**Note**  
If you skip this step, you might get a `Device not ready` error when you try to access the Home Folder\.

At any point, if you have trouble signing into AppStream 2\.0, you can restart your AppStream 2\.0 fleet and try to sign in again, using the following steps\.

1. Open the AppStream 2\.0 console\.

1. In the left navigation, choose **Fleets**\.

1. Choose the fleet you are trying to use\.

1. Choose **Action**, then choose **Stop**\.

1. Wait for the fleet to stop\.

1. Choose **Action**, then choose **Start**\.

This process can take around 10 minutes\.

## Step 5: Verify buckets in Amazon S3 \(optional\)<a name="tutorial-aas-step5"></a>

One of the tasks completed by the AWS CloudFormation template you used to create the stack was to create two buckets in Amazon S3, which are necessary to save and restore user data and application settings across work sessions\. These buckets are as follows:
+ Name starts with `appstream2-`\. This bucket maps data to your Home Folder in AppStream 2\.0 \(`D:\PhotonUser\My Files\Home Folder`\)\.
**Note**  
The Home Folder is unique for a given email address and is shared across all fleets and stacks in a given AWS account\. The name of the Home Folder is a SHA256 hash of the user’s email address, and is stored on a path based on that hash\.
+ Name starts with `appstream-app-settings-`\. This bucket contains user session information for AppStream 2\.0, and includes settings such as browser favorites, IDE and application connection profiles, and UI customizations\. For more information, see [How Application Settings Persistence Works](https://docs.aws.amazon.com/appstream2/latest/developerguide/how-it-works-app-settings-persistence.html) in the *Amazon AppStream 2\.0 Administration Guide*\.

To verify that the buckets were created, follow these steps:

1. Open the Amazon S3 console\.

1. In the left navigation, choose **Buckets**\.

1. In **Find buckets by name**, enter **appstream** to filter the list\.

If you see the buckets, no further action is necessary\. Just be aware that the buckets exist\. If you do not see the buckets, then either the AWS CloudFormation template is not finished running, or an error occurred\. Go to the AWS CloudFormation console and review the stack creation messages\.

## Next steps<a name="tutorial-aas-next-steps"></a>

Now that the AppStream 2\.0 infrastructure is set up, you can set up and start using Enterprise Analyzer\. For more information, see [Tutorial: Set up Enterprise Analyzer on AppStream 2\.0](set-up-ea.md)\. You can also set up Enterprise Developer\. For more information, see [Tutorial: Set up Micro Focus Enterprise Developer on AppStream 2\.0](set-up-ed.md)\.

## Clean up resources<a name="tutorial-aas-cleanup"></a>

The procedure to clean up the created stack and fleets is described in [Create an AppStream 2\.0 Fleet and Stack](https://docs.aws.amazon.com/appstream2/latest/developerguide/set-up-stacks-fleets.html)\.

When the AppStream 2\.0 objects have been deleted, the account administrator can also, if appropriate, clean up the Amazon S3 buckets for Application Settings and Home Folders\.

**Note**  
The home folder for a given user is unique across all fleets, so you might need to retain it if other AppStream 2\.0 stacks are active in the same account\.

Finally, AppStream 2\.0 does not currently allow you to delete users using the console\. Instead, you must use the service API with the CLI\. For more information, see [User Pool Administration](https://docs.aws.amazon.com/appstream2/latest/developerguide/user-pool-admin.html) in the *Amazon AppStream 2\.0 Administration Guide*\.