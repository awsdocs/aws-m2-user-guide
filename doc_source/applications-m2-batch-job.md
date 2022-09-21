# Submit batch jobs for AWS Mainframe Modernization applications<a name="applications-m2-batch-job"></a>

In AWS Mainframe Modernization you can submit batch jobs for your applications\. You can submit or cancel batch jobs and review details about batch job executions\. Each time you submit a batch job, AWS Mainframe Modernization creates a separate batch job execution, which you can monitor\. You can search for batch jobs by name, supply JCL or script files to batch jobs, and provide parameters to batch jobs\.

**Important**  
Cancelling a batch job does not delete the job\. It cancels a particular execution of the batch job\. The batch job records remain available for you to view in the details for the batch job execution\.

Your application must be successfully deployed and in the `Running` state for you to submit a batch job\.

Use the AWS Mainframe Modernization console  to import data sets for a AWS Mainframe Modernization application\.

These instructions assume that you have completed the steps in [Setting up AWS Mainframe Modernization](setting-up.md) and in [Create a AWS Mainframe Modernization application](applications-m2-create.md)\.

## Submit a batch job<a name="applications-m2-batch-job-submit.console"></a>

**To submit a batch job**

1. Open the AWS Mainframe Modernization console at [https://console\.aws\.amazon\.com/m2/](https://console.aws.amazon.com/m2/)\.

1. In the region selector, choose the AWS Region where the application you want to submit a batch job for was created\.

1. On the **Applications** page, choose the application you want to submit a batch job for\.

1. On the application details page, choose **Batch jobs**\.

1. Choose **Submit job**\.