# Submit batch jobs for AWS Mainframe Modernization applications<a name="applications-m2-batch-job"></a>

In AWS Mainframe Modernization you can submit batch jobs for your applications\. You can submit or cancel batch jobs and review details about batch job executions\. Each time that you submit a batch job, AWS Mainframe Modernization creates a separate batch job execution\. You can monitor this job execution\. You can search for batch jobs by name and supply JCL or script files to batch jobs\.

**Important**  
If you cancel a batch job, this doesn't delete the job\. It cancels a particular execution of the batch job\. The batch job records remain available for you to view in the details for the batch job execution\.

If your batch job requires access to one or more data sets, use the AWS Mainframe Modernization console or the AWS Command Line Interface \(AWS CLI\) to import the data sets\. For more information, see [Import data sets for AWS Mainframe Modernization applications](applications-m2-dataset.md)\.

These instructions assume that you have completed the steps in [Setting up AWS Mainframe Modernization](setting-up.md) and in [Create an AWS Mainframe Modernization application](applications-m2-create.md)\.

## Submit a batch job<a name="applications-m2-batch-job-submit.console"></a>

**To submit a batch job**

1. Open the AWS Mainframe Modernization console at [https://console\.aws\.amazon\.com/m2/](https://console.aws.amazon.com/m2/)\.

1. In the AWS Region selector, choose the Region where the application that you want to submit a batch job for was created\.

1. On the **Applications** page, choose the application that you want to submit a batch job for\.
**Note**  
Before you can submit a batch job to an application, you must deploy the application successfully\.

1. On the application details page, choose **Batch jobs**\.

1. Choose **Submit job**\.

1. In the **Select a script** section, choose a script\. You can search for the script that you want by name\.

1. Choose **Submit job**\.