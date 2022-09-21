# Update a AWS Mainframe Modernization application<a name="applications-m2-update"></a>

Use the AWS Mainframe Modernization console  to update a AWS Mainframe Modernization application\.

These instructions assume that you have completed the steps in [Setting up AWS Mainframe Modernization](setting-up.md)\.

## Update an application<a name="applications-m2-update-console"></a>

A AWS Mainframe Modernization application can have multiple versions, each with its own application definition\. You update an application by providing a new application definition, and as a result, a new version of the application is created\.

**To update an application**

1. Open the AWS Mainframe Modernization console at [https://console\.aws\.amazon\.com/m2/](https://console.aws.amazon.com/m2/)\.

1. In the region selector, choose the AWS Region where the application you want to update was created\.

1. On the **Applications** page, choose the application you want to update\.

1. On the application details page, in **Current definition**, choose **Edit** to update the current application definition\.

1. On the **Update application** page, use the inline editor to update the current application definition or choose **Use an application definition JSON file in an Amazon S3 bucket** and provide the location of the application definition you want to use\. For more information, see [Blu Age application definition sample](applications-m2-definition.md#applications-m2-definition-ba) or [Micro Focus application definition](applications-m2-definition.md#applications-m2-definition-mf)\.

1. When you are finished updating the application definition, choose **Update**\.

In addition to updating the application definition, you can submit batch jobs for the application, import data sets, or add more tags\. For more information, see:
+ [Submit batch jobs for AWS Mainframe Modernization applications](applications-m2-batch-job.md)
+ [Import data sets for AWS Mainframe Modernization applications](applications-m2-dataset.md)