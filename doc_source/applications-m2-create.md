# Create an AWS Mainframe Modernization application<a name="applications-m2-create"></a>

Use the AWS Mainframe Modernization console  to create an AWS Mainframe Modernization application\. 

These instructions assume that you have completed the steps in [Setting up AWS Mainframe Modernization](setting-up.md)\.

## Create an application<a name="applications-m2-create-console"></a>

**To create an application**

1. Open the AWS Mainframe Modernization console at [https://console\.aws\.amazon\.com/m2/](https://console.aws.amazon.com/m2/)\.

1. In the AWS Region selector, choose the Region where you want to create the application\.

1. On the **Applications** page, choose **Create application**\.

1. On the **Specify basic information** page, in the **Name and description** section, enter a name for the application\.

1. \(Optional\) In the **Application description** field, enter a description for the application\. This description can help you and other users identify the purpose of the application\.

1. In the **Engine type** section, choose **Blu Age** for automated refactoring, or **Micro Focus** for replatforming\.

1. In the **KMS key** section, choose **Customize encryption settings** if you want to use a customer managed AWS KMS key\. For more information, see [Data encryption at rest for AWS Mainframe Modernization service](data-protection.md#encryption-rest)\.
**Note**  
By default, AWS Mainframe Modernization encrypts your data with a AWS KMS key that AWS Mainframe Modernization owns and manages for you\. However, you can choose to use a customer managed AWS KMS key\.

1. \(Optional\) Choose an AWS KMS key by name or Amazon Resource Name \(ARN\), or choose **Create an AWS KMS key** to go to the AWS KMS console and create a new AWS KMS key\.

1. \(Optional\) In the **Tags** section, choose **Add new tag** to add one or more application tags to your application\. An application tag is a custom attribute label that helps you organize and manage your AWS resources\)\.

1. Choose **Next**\.

1. In the **Resources and configurations** section, use the inline editor to enter the application definition\. Alternatively, choose **Use an application definition JSON file in an Amazon S3 bucket** and provide the location of the application definition that you want to use\. For more information, see [Blu Age application definition sample](applications-m2-definition.md#applications-m2-definition-ba) or [Micro Focus application definition](applications-m2-definition.md#applications-m2-definition-mf)\.

1. Choose **Next**\.

1. On the **Review and create** page, review the information that you entered, and then choose **Create application**\.