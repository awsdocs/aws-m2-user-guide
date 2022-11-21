# Cannot access an application's URL<a name="both-application-connectivity"></a>
+ Engine: Blu Age and Micro Focus
+ Component: applications

If you cannot access the URL for a running AWS Mainframe Modernization application that you created and deployed to an AWS Mainframe Modernization runtime environment, you might need to configure the inbound rules on the security group that you associated with the runtime environment\.

## How this error occurs<a name="both-application-connectivity-how"></a>

When you create a runtime environment, the security group you provide, including the default security group, must have inbound rules configured to allow traffic to the deployed applications from outside the VPC, if you want to allow this type of access\.

## How do you know if this is your situation?<a name="both-application-connectivity-situation"></a>

The application started successfully and is running normally, but you are unable to connect to it using its URL\.

## What can you do?<a name="both-application-connectivity-actions"></a>

Check whether the Amazon VPC security group associated with the runtime environment allows traffic to the environment on the appropriate application ports\. To check the security group rules, complete the following steps:

1. Open the AWS Mainframe Modernization console at [https://console\.aws\.amazon\.com/m2/](https://us-west-2.console.aws.amazon.com/m2/home?region=us-west-2#/)\.

1. In the left navigation, choose **Environments**\.

1. Choose the runtime environment that hosts the application you want to connect to\.

1. Choose **Configurations**\.

1. In **Security & Network**, choose the security group\. The link opens the details of the security group in the Amazon VPC console\.

1. If necessary, choose **Edit inbound rules** and add the following rule if not already present:  
Type  
Custom TCP  
Port  
8196 or the port that matches the listener properties specified in the application definition\. For more information, see [Step 2: Create the application definition](tutorial-runtime-ba.md#tutorial-runtime-ba-step2)\.  
Source  
The IP address from where you are calling the application\. You can choose **myIP** from the dropdown\. If you still have timeout issues, try choosing **Anywhere IPV4** or **Anywhere IPV6**\. Make sure to stop the application and start it again after you add the inbound rule on the security group\.

   For more information, see [Work with security group rules](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html#working-with-security-group-rules) in *Amazon VPC User Guide*\.