# Security in AWS Mainframe Modernization<a name="security"></a>

Cloud security at AWS is the highest priority\. As an AWS customer, you benefit from a data center and network architecture that is built to meet the requirements of the most security\-sensitive organizations\.

Security is a shared responsibility between AWS and you\. The [shared responsibility model](http://aws.amazon.com/compliance/shared-responsibility-model/) describes this as security of the cloud and security in the cloud:
+ **Security of the cloud** – AWS is responsible for protecting the infrastructure that runs AWS services in the AWS Cloud\. AWS also provides you with services that you can use securely\. Third\-party auditors regularly test and verify the effectiveness of our security as part of the [AWS Compliance Programs](http://aws.amazon.com/compliance/programs/)\. To learn about the compliance programs that apply to AWS Mainframe Modernization, see [AWS Services in Scope by Compliance Program](http://aws.amazon.com/compliance/services-in-scope/)\.
+ **Security in the cloud** – Your responsibility is determined by the AWS service that you use\. You are also responsible for other factors including the sensitivity of your data, your company’s requirements, and applicable laws and regulations 

This documentation helps you understand how to apply the shared responsibility model when using AWS Mainframe Modernization\. It shows you how to configure AWS Mainframe Modernization to meet your security and compliance objectives\. You also learn how to use other AWS services that help you to monitor and secure your AWS Mainframe Modernization resources\.

AWS Mainframe Modernization provides its own IAM\-protected resources \(application, environment, deployment etc\), which are the AWS Mainframe Modernization administrative resources, on which any action must be allowed by IAM policies\.

AWS Mainframe Modernization for replatforming using is also secured by IAM\. IAM grants or denies permission to a principal for a specific action on a defined resource, derived from the original mainframe environment, through standard IAM policies as well\. The AWS Mainframe Modernization replatforming runtime calls the IAM authorization service when an application attempts such action on a protected resource\. IAM will return allow or deny based on standard IAM policy evaluation mechanisms\.

**Topics**
+ [Data protection](data-protection.md)
+ [Identity and Access Management](security-iam.md)
+ [Compliance validation](compliance-validation.md)
+ [Resilience](disaster-recovery-resiliency.md)
+ [Infrastructure security](infrastructure-security.md)
+ [Tutorial: Blu Age Application Security in AWS Mainframe Modernization](security-app-ba.md)
+ [AWS PrivateLink](vpc-interface-endpoints.md)