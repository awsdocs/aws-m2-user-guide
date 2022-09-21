# Tutorial: Managed Runtime for Blu Age<a name="tutorial-runtime-ba"></a>

This tutorial demonstrates how to deploy a Blu Age\-modernized application into an AWS Mainframe Modernization runtime environment\.

**Topics**
+ [Prerequisites](#tutorial-runtime-ba-prerequisites)
+ [Step 1: Upload the demo application](#tutorial-runtime-ba-step1)
+ [Step 2: Create the application definition](#tutorial-runtime-ba-step2)
+ [Step 3: Create a runtime environment](#tutorial-runtime-ba-step3)
+ [Step 4: Create an application](#tutorial-runtime-ba-step4)
+ [Step 5: Deploy an application](#tutorial-runtime-ba-step5)
+ [Step 6: Start an application](#tutorial-runtime-ba-step6)
+ [Step 7: Access the application](#tutorial-runtime-ba-step7)
+ [Clean up resources](#tutorial-runtime-ba-clean)
+ [Next steps](#tutorial-runtime-ba-next)

## Prerequisites<a name="tutorial-runtime-ba-prerequisites"></a>

This tutorial requires the following prerequisites:
+ The demo application archive [PlanetsDemo\-v1\.zip](https://d3lkpej5ajcpac.cloudfront.net/demo/bluage/PlanetsDemo-v1.zip)\.

## Step 1: Upload the demo application<a name="tutorial-runtime-ba-step1"></a>

Upload the demo application to an Amazon S3 bucket\. Make sure that this bucket is in the same region where you will deploy the application\. The following example shows a bucket named **planetsdemo**, with a key prefix \(folder\) named **v1** and an archive named `planetsdemo-v1.zip`:

![\[The planetsdemo bucket in Amazon S3 showing the v1 prefix and the planetsdemo-v1.zip file.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/s3-ba-bucket.png)

## Step 2: Create the application definition<a name="tutorial-runtime-ba-step2"></a>

Deploying an application to the managed runtime requires a AWS Mainframe Modernization application definition, which is a JSON file describing the application location and settings\. The following example is such an application definition for the demo application:

```
{
  "resources": [
    {
      "resource-type": "listener",
      "resource-id": "tomcat",
      "properties": {
        "port": 8196,
        "type": "http"
      }
    },
    {
      "resource-type": "ba-application",
      "resource-id": "planetsdemo",
      "properties": {
        "app-location": "${s3-source}/PlanetsDemo-v1.zip"
      }
    }
  ],
  "source-locations": [
    {
      "source-id": "s3-source",
      "source-type": "s3",
      "properties": {
        "s3-bucket": "planetsdemo",
        "s3-key-prefix": "v1"
      }
    }
  ]
}
```

Change the `ba-application` and `s3-source` entries as needed to point to the Amazon S3 bucket where you previously uploaded the sample application\.

For more information on the application definition, see [Blu Age application definition sample](applications-m2-definition.md#applications-m2-definition-ba)\.

## Step 3: Create a runtime environment<a name="tutorial-runtime-ba-step3"></a>

Create the AWS Mainframe Modernization runtime environment by following these steps:

1. Navigate to **AWS Mainframe Modernization** in the AWS Management Console\.

1. In **Modernize mainframe applications**, choose **Refactor with Blu Age** then choose **Get started**\.  
![\[The AWS Mainframe Modernization get started page showing Refactor with Blu Age.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/m2-ba-get-started.png)

1. In **How would you like to start with AWS Mainframe Modernization**, choose **Deploy** and **Create runtime environment**\.  
![\[The AWS Mainframe Modernization get started page with deploy and create runtime environment selected.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/update-m2-ba-deploy-create-env.png)

1. Choose **Environments**, then choose **Create environment**\. In **Specify basic information**, enter a name and description for your environment, then make sure **AWS Blu Age** engine is selected\. You may optionally add tags to the created resource\. Then choose **Next**\.  
![\[The AWS Mainframe Modernization specify basic information with the AWS Blu Age engine selected.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/m2-ba-env-basic.png)

1. In **Specify configurations**, choose **Standalone runtime environment**:  
![\[The AWS Mainframe Modernization Availability section with standalone runtime environment selected.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/m2-ba-config-avail.png)

1. In **Security and network**, choose **Allow applications deployed to this environment to be publicly accessible**, and choose a VPC \(you can use the **Default** one\) and two subnets:  
![\[The AWS Mainframe Modernization Security and Network section with the default VPC and two subnets selected.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/m2-ba-security-network.png)

   If you want to access the application from outside the VPC you chose, make sure that the inbound rules for that VPC are configured properly\. For more information, see [Cannot access an application's URL](both-application-connectivity.md)\.

1. In **Attach storage \- Optional**, leave the defaults and choose **Next**:  
![\[The AWS Mainframe Modernization Attach storage page with the default values applied.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/m2-ba-attach-storage.png)

1. In **Review and create**, review the information, then choose **Create environment**\.

## Step 4: Create an application<a name="tutorial-runtime-ba-step4"></a>

1. Navigate to **AWS Mainframe Modernization** in the AWS Management Console\.

1. Choose **Applications**, then choose **Create application**\. In **Specify basic information**, enter a name and description for the application, and make sure **AWS Blu Age** engine is selected\. Then choose **Next**\.  
![\[The AWS Mainframe Modernization application specify basic information section, with the AWS Blu Age engine selected.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/m2-ba-app-basic.png)

1. In **Specify resources and configurations**, copy and paste the previously updated application definition JSON:  
![\[The AWS Mainframe Modernization resources and configurations section with the updated application definition JSON pasted in.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/m2-ba-resources-configs.png)

1. In **Review and create**, review your choices, then choose **Create application**\.

## Step 5: Deploy an application<a name="tutorial-runtime-ba-step5"></a>

After you successfully create both the AWS Mainframe Modernization runtime environment and application \(both must be in the **Available** state\), you can deploy the application into the runtime environment\. To do this, complete the following steps:

1. Navigate to **AWS Mainframe Modernization** in the AWS Management Console and choose **Environments**\.  
![\[The AWS Mainframe Modernization runtime environments list with the previously created runtime environment displayed.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/m2-ba-environments.png)

1. Choose the previously created runtime environment:  
![\[The AWS Mainframe Modernization environment details page for the planets-demo-env environment.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/m2-ba-env-details-planets.png)

1. Choose **Deploy application**:  
![\[The AWS Mainframe Modernization deploy application page with the planets demo app displayed.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/m2-ba-app-deploy.png)

1. Choose the previously created application, then choose **Deploy**\.

1. Wait until the application finishes deploying\. You'll see a banner with the message **Application was deployed successfully**\.

## Step 6: Start an application<a name="tutorial-runtime-ba-step6"></a>

1. Navigate to **AWS Mainframe Modernization** in the AWS Management Console and choose **Applications**\.

1. Choose your application, then scroll down to **Deployments**: it should be in the **Succeeded** state:  
![\[The AWS Mainframe Modernization deployments page showing the deployment status of succeeded.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/m2-ba-app-deployments-succeeded.png)

1. Choose **Actions**, then choose **Start application**\.

## Step 7: Access the application<a name="tutorial-runtime-ba-step7"></a>

1. Wait until the application is in the **Running** state\. You'll see a banner with the message **Application was started successfully**\.

1. Copy the application DNS hostname, which you can find in the **Application information** section of the application\.

1. In a browser, navigate to `http://{hostname}:{portname}/PlanetsDemo-web-1.0.0/`, where:
   + `hostname` is the DNS name copied above\.
   + `portname` is the tomcat port defined in the application definition earlier\.

If you cannot access the application, see [Cannot access an application's URL](both-application-connectivity.md)\.

## Clean up resources<a name="tutorial-runtime-ba-clean"></a>

If you no longer need the resources you created for this tutorial, delete them so that you won't continue to be charged for them\. Complete the following steps:
+ If the AWS Mainframe Modernization application is still running, stop it\.
+ Delete the application\. For more information, see [Delete a AWS Mainframe Modernization application](applications-m2-delete.md)\.
+ Delete the runtime environment\. For more information, see [Delete a AWS Mainframe Modernization runtime environmentDelete a runtime environment](delete-environments-m2.md)\.

## Next steps<a name="tutorial-runtime-ba-next"></a>

If you would like to continue learning, you can explore the following tutorials:
+ [Tutorial: Setting up the build for the Planets demo app](tutorial-build-ba.md)
+ [Tutorial: Setting up a CI/CD pipeline for use with Blu Age Developer](tutorial-cicd-ba.md)