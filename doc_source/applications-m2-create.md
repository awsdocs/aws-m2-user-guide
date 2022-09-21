# Create a AWS Mainframe Modernization application<a name="applications-m2-create"></a>

Use the AWS Mainframe Modernization console  to create a AWS Mainframe Modernization application\. 

These instructions assume that you have completed the steps in [Setting up AWS Mainframe Modernization](setting-up.md)\.

## Create an application<a name="applications-m2-create-console"></a>

**To create an application**

1. Open the AWS Mainframe Modernization console at [https://console\.aws\.amazon\.com/m2/](https://console.aws.amazon.com/m2/)\.

1. In the region selector, choose the AWS Region where you want to create the application\.

1. On the **Applications** page, choose **Create application**\.

1. On the **Specify basic information** page, in **Name and description** name, enter a name for the application\.

1. \(Optional\) In **Application description**, enter a description for the application\. This can help you and other users identify the purpose of the application\.

1. In **Engine type**, choose **Blu Age** for automated refactoring or **Micro Focus** for replatforming\.

1. In **KMS key**, choose **Customize encryption settings** if you want to use a customer managed AWS KMS key\. For more information, see [Encryption at rest](data-protection.md#encryption-rest)\.
**Note**  
By default, AWS Mainframe Modernization encrypts your data with a AWS KMS key that AWS Mainframe Modernization owns and manages for you\. However, you can choose to use a customer managed AWS KMS key\.

1. \(Optional\) Choose an AWS KMS key by name or Amazon Resource Name \(ARN\), or choose **Create an AWS KMS key** to go to the AWS KMS console to create a new AWS KMS key\.

1. \(Optional\) In **Tags**, Choose **Add new tag** to add one or more application tags \(a custom attribute label that helps you organize and manage your AWS resources\) to your application\.

1. Choose **Next**\.

1. In **Specify resources and configurations**, use the inline editor to enter the application definition or choose **Use an application definition JSON file in an Amazon S3 bucket** and provide the location of the application definition you want to use\. For more information, see [Blu Age application definition sample](applications-m2-definition.md#applications-m2-definition-ba) or [Micro Focus application definition](applications-m2-definition.md#applications-m2-definition-mf)\.

1. Choose **Next**\.

1. In **Review and create**, review the information you entered, then choose **Create application**\.