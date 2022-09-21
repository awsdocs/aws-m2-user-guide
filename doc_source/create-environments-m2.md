# Create a AWS Mainframe Modernization runtime environment<a name="create-environments-m2"></a>

Use the AWS Mainframe Modernization console  to create a AWS Mainframe Modernization environment\. 

These instructions assume that you have completed the steps in [Setting up AWS Mainframe Modernization](setting-up.md)\.

## Create a runtime environment<a name="create-environments-m2.console"></a>

**To create a runtime environment**

1. Open the AWS Mainframe Modernization console at [https://console\.aws\.amazon\.com/m2/](https://console.aws.amazon.com/m2/)\.

1. In the region selector, choose the AWS Region where you want to create the environment\.

1. On the **Environments** page, choose **Create environment**\.

1. On the **Specify basic information** page, in **Name and description** name, enter a name for the environment\.

1. \(Optional\) In **Environment description**, enter a description for the environment\. This can help you and other users identify the purpose of the runtime environment\.

1. In **Engine options**, choose **Blu Age** for automated refactoring or **Micro Focus** for replatforming\.

1. Choose a version for the engine you chose\.

1. \(Optional\) In **Tags**, Choose **Add new tag** to add one or more environment tags \(a custom attribute label that helps you organize and manage your AWS resources\) to your environment\.

1. Choose **Next**\.

1. In **Availability**, choose **Standalone runtime environment** or **High availability cluster**\.

   The availability pattern determines how available your application will be when it is running\. Standalone is fine for development purposes\. High availability is for applications that must be available at all times\.

1. In **Resources**, choose an instance type and desired capacity\.

   These resources are the AWS Mainframe Modernization\-managed Amazon EC2 instances that will host your runtime environment\. Standalone runtime environments have two choices for instance type, and permit only one instance\. High availability runtime environments have two choices for instance type and permit up to two instances\.

   For more information, see [Amazon EC2 Instance Types](http://aws.amazon.com/ec2/instance-types/), and contact an AWS mainframe specialist for guidance\.

1. In **Security and network**, do the following:

   1. Choose **Allow applications deployed to this environment to be publicly accessible** if that is what you want\.

   1. Choose a VPC\.

   1. Choose one or more subnets, if you are using the high availability pattern\.

   1. Choose a security group for the VPC you chose\.

   1. In **KMS key**, choose **Customize encryption settings** if you want to use a customer managed AWS KMS key\. For more information, see [Encryption at rest](data-protection.md#encryption-rest)\.
**Note**  
By default, AWS Mainframe Modernization encrypts your data with a AWS KMS key that AWS Mainframe Modernization owns and manages for you\. However, you can choose to use a customer managed AWS KMS key\.

   1. \(Optional\) Choose an AWS KMS key by name or Amazon Resource Name \(ARN\), or choose **Create an AWS KMS key** to go to the AWS KMS console to create a new AWS KMS key\.

1. Choose **Next**\.

1. \(Optional\) In **Attach storage**, choose one or more Amazon EFS or Amazon FSx file systems\.

1. Choose **Next**\.

1. In **Review and create**, review the information you entered, then choose **Create environment**\.