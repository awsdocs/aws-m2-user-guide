# Import data sets for AWS Mainframe Modernization applications<a name="applications-m2-dataset"></a>

In AWS Mainframe Modernization you can import data sets to use with your applications\. You can specify the data sets in a JSON file stored in an Amazon S3 bucket, or you can specify data set configuration values separately\. After you import the data sets, you can review the details of the import task to confirm that the data sets you wanted were imported\. All cataloged data sets for an application are listed together in the console\.

Use the AWS Mainframe Modernization console  to import data sets for a AWS Mainframe Modernization application\.

These instructions assume that you have completed the steps in [Setting up AWS Mainframe Modernization](setting-up.md) and in [Create a AWS Mainframe Modernization application](applications-m2-create.md)\.

## Import a data set<a name="applications-m2-dataset-import.console"></a>

**To import a data set**

1. Open the AWS Mainframe Modernization console at [https://console\.aws\.amazon\.com/m2/](https://console.aws.amazon.com/m2/)\.

1. In the region selector, choose the AWS Region where the application you want to import data sets for was created\.

1. On the **Applications** page, choose the application you want to import data sets for\.

1. On the application details page, choose **Data sets**\.

1. Choose **Import**\.

1. Do one of the following:
   + Choose **Use data set configuration JSON file in an Amazon S3 bucket** and provide the location of the data set configuration\.
   + Choose **Specify the data set configuration values separately**, then choose **Add new item**\.

     Enter the name, location, and External S3 location for each data set configuration value\.

1. Choose **Submit**\.