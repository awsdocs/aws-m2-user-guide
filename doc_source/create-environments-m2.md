# Create an AWS Mainframe Modernization runtime environment<a name="create-environments-m2"></a>

Use the AWS Mainframe Modernization console  to create an AWS Mainframe Modernization environment\. 

These instructions assume that you've completed the steps in [Setting up AWS Mainframe Modernization](setting-up.md)\.

## Create a runtime environment<a name="create-environments-m2.console"></a>

**To create a runtime environment**

1. Open the AWS Mainframe Modernization console at [https://console\.aws\.amazon\.com/m2/](https://console.aws.amazon.com/m2/)\.

1. In the AWS Region selector, choose the Region where you want to create the environment\.

1. On the **Environments** page, choose **Create environment**\.

1. On the **Specify basic information** page, provide the following information:

   1. In the **Name and description** section, enter a name for the environment\.

   1. \(Optional\) In the **Environment description** field, enter a description for the environment\. This description can help you and other users identify the purpose of the runtime environment\.

   1. In the **Engine options** section, choose **Blu Age** for automated refactoring, or **Micro Focus** for replatforming\.

   1. Choose a version for the engine that you selected\.

   1. \(Optional\) In the **Tags** section, choose **Add new tag** to add one or more environment tags to your environment\. An environment tag is a custom attribute label that helps you organize and manage your AWS resources\.

   1. Choose **Next**\.

1. On the **Specify configurations** page, provide the following information:

   1. In the **Availability** section, choose **Standalone runtime environment** or **High availability cluster**\.

      The availability pattern determines how available your application will be when it runs\. *Standalone* is fine for development purposes\. *High availability* is for applications that must be available at all times\.

   1. In **Resources**, choose an instance type and desired capacity\.

      These resources are the AWS Mainframe Modernization managed Amazon EC2 instances that will host your runtime environment\. Standalone runtime environments offer two choices for instance type and permit only one instance\. High availability runtime environments offer two choices for instance type and permit up to two instances\.

      For more information, see [Amazon EC2 Instance Types](http://aws.amazon.com/ec2/instance-types/), and contact an AWS mainframe specialist for guidance\.

1. In the **Security and network** section, do the following:

   1. If you want the applications to be publicly accessible, choose **Allow applications deployed to this environment to be publicly accessible**\.

   1. Choose a VPC\.

   1. If you're using the high availability pattern, choose two or more subnets\. If you're using the standalone pattern with the Blu Age engine, choose two or more subnets\. If you're using the standalone pattern with the Micro Focus engine, you can specify one subnet\.

   1. Choose a security group for the VPC that you selected\.
**Note**  
AWS Mainframe Modernization creates a Network Load Balancer for you to distribute connections to your runtime environment\. Make sure your security group inbound rules allow access from an IP address to the port you specified in the `listener` property of the application definition\. For more information, see [Register targets](https://docs.aws.amazon.com/elasticloadbalancing/latest/network/target-group-register-targets.html#target-security-groups) in the *User Guide for Network Load Balancers*\.

   1. In the **KMS key** field, choose **Customize encryption settings** if you want to use a customer managed AWS KMS key\. For more information, see [Data encryption at rest for AWS Mainframe Modernization service](data-protection.md#encryption-rest)\.
**Note**  
By default, AWS Mainframe Modernization encrypts your data with a AWS KMS key that AWS Mainframe Modernization owns and manages for you\. However, you can choose to use a customer managed AWS KMS key\.

   1. \(Optional\) Choose an AWS KMS key by name or Amazon Resource Name \(ARN\)\. Alternately, choose **Create an AWS KMS key** to go to the AWS KMS console and create a new AWS KMS key\.

   1. Choose **Next**\.

1. \(Optional\) On the **Attach storage** page, choose one or more Amazon EFS or Amazon FSx file systems, and then choose **Next**\.

1. In the **Maintenance window** section, choose when you want to apply pending changes to the environment\.
   + If you choose **No preference**, AWS Mainframe Modernization chooses an optimized maintenance window for you\.
   + If you want to specify a particular maintenance window, choose **Select new maintenance window**\. Then choose a day of the week, a start time, and a duration for the maintenance window\.

   For more information about the maintenance window, see [AWS Mainframe Modernization maintenance window](update-environments-m2.md#update-environments-m2-maintenance)\.

   Choose **Next**\.

1. On the **Review and create** page, review the information that you entered, and then choose **Create environment**\.