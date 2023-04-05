# Tutorial: Managed Runtime for Blu Age<a name="tutorial-runtime-ba"></a>

This tutorial shows how to deploy a Blu Age modernized application into an AWS Mainframe Modernization runtime environment\.

**Topics**
+ [Prerequisites](#tutorial-runtime-ba-prerequisites)
+ [Step 1: Upload the demo application](#tutorial-runtime-ba-step1)
+ [Step 2: Create the application definition](#tutorial-runtime-ba-step2)
+ [Step 3: Create a runtime environment](#tutorial-runtime-ba-step3)
+ [Step 4: Create an application](#tutorial-runtime-ba-step4)
+ [Step 5: Deploy an application](#tutorial-runtime-ba-step5)
+ [Step 6: Start an application](#tutorial-runtime-ba-step6)
+ [Step 7: Access the application](#tutorial-runtime-ba-step7)
+ [Step 8: Test the application](#tutorial-runtime-ba-test)
+ [Clean up resources](#tutorial-runtime-ba-clean)
+ [Next steps](#tutorial-runtime-ba-next)

## Prerequisites<a name="tutorial-runtime-ba-prerequisites"></a>

To complete this tutorial, download the demo application archive [PlanetsDemo\-v1\.zip](https://d3lkpej5ajcpac.cloudfront.net/demo/bluage/PlanetsDemo-v1.zip)\.

The running demo application requires a modern browser for access\. Whether you run this browser from your desktop or from an Amazon Elastic Compute Cloud instance, for example, within the VPC, determines your security settings\.

## Step 1: Upload the demo application<a name="tutorial-runtime-ba-step1"></a>

Upload the demo application to an Amazon S3 bucket\. Make sure that this bucket is in the same AWS Region where you will deploy the application\. The following example shows a bucket named **planetsdemo**, with a key prefix, or folder, named **v1** and an archive named `planetsdemo-v1.zip`\.

![\[The planetsdemo bucket in Amazon S3 showing the v1 prefix and the planetsdemo-v1.zip file.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/s3-ba-bucket.png)

**Note**  
The folder in the bucket is required\.

## Step 2: Create the application definition<a name="tutorial-runtime-ba-step2"></a>

To deploy an application to the managed runtime, you need an AWS Mainframe Modernization application definition\. This definition is a JSON file that describes the application location and settings\. The following example is such an application definition for the demo application:

```
{
    "template-version": "2.0",
    "source-locations": [{
        "source-id": "s3-source",
        "source-type": "s3",
        "properties": {
            "s3-bucket": "planetsdemo",
            "s3-key-prefix": "v1"
        }
    }],
    "definition": {
        "listeners": [{
            "port": 8196,
            "type": "http"
        }],
        "ba-application": {
            "app-location": "${s3-source}/PlanetsDemo-v1.zip"
        }
    }
}
```

Change the `s3-bucket` entry to the name of the bucket where you stored the sample application zip file\.

For more information on the application definition, see [Blu Age application definition sample](applications-m2-definition.md#applications-m2-definition-ba)\.

## Step 3: Create a runtime environment<a name="tutorial-runtime-ba-step3"></a>

To create the AWS Mainframe Modernization runtime environment, perform the following steps:

1. Open the AWS Mainframe Modernization console at [https://console\.aws\.amazon\.com/m2/](https://console.aws.amazon.com/m2/)\.

1. In the AWS Region selector, choose the Region where you want to create the environment\. This AWS Region must match the Region where you created the S3 bucket in [Step 1: Upload the demo application](#tutorial-runtime-ba-step1)\. 

1. Under **Modernize mainframe applications**, choose **Refactor with Blu Age**, and then choose **Get started**\.  
![\[The Modernize mainframe applications section with Refactor with Blu Age selected.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/m2-ba-get-started.png)

1. Under **How would you like to start with AWS Mainframe Modernization**, choose **Deploy** and **Create runtime environment**\.  
![\[The How would you like to start with &M2; section with deploy and create runtime environment selected.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/update-m2-ba-deploy-create-env.png)

1. In the left navigation, choose **Environments**, then choose **Create environment**\. On the **Specify basic information** page, enter a name and description for your environment, and then make sure **AWS Blu Age** engine is selected\. Optionally, you can add tags to the created resource\. Then choose **Next**\.  
![\[The AWS Mainframe Modernization Specify basic information page with the AWS Blu Age engine selected.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/m2-ba-env-basic.png)

1. On the **Specify configurations** page, choose **Standalone runtime environment**\.  
![\[The AWS Mainframe Modernization Availability section with Standalone runtime environment selected.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/m2-ba-config-avail.png)

1. Under **Security and network**, make the following changes:
   + Choose **Allow applications deployed to this environment to be publicly accessible**\. This option assigns a public IP address to the application so that you can access it from your desktop\.
   + Choose a VPC\. You can use the **Default**\.
   + Choose two subnets\. Make sure that the subnets allow the assignment of public IP addresses\.
   + Choose a security group\. You can use the **Default**\. Make sure that the security group that you choose allows access from the browser IP address to the port you specified in the `listener` property of the application definition\. For more information, see [Step 2: Create the application definition](#tutorial-runtime-ba-step2)\.  
![\[The AWS Mainframe Modernization Security and network section with the default VPC and two subnets selected.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/m2-ba-security-network.png)

   If you want to access the application from outside the VPC that you chose, make sure that the inbound rules for that VPC are configured properly\. For more information, see [Cannot access an application's URL](both-application-connectivity.md)\.

1. Choose **Next**\.

1. In **Attach storage \- Optional**, leave the default selections and choose **Next**\.  
![\[The AWS Mainframe Modernization Attach storage page with the default values applied.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/m2-ba-attach-storage.png)

1. In **Schedule maintenance**, choose **No preference**, and then choose **Next**\.

1. In **Review and create**, review the information, and then choose **Create environment**\.

## Step 4: Create an application<a name="tutorial-runtime-ba-step4"></a>

1. Navigate to **AWS Mainframe Modernization** in the AWS Management Console\.

1. In the navigation pane, choose **Applications**, and then choose **Create application**\. On the **Specify basic information** page, enter a name and description for the application, and make sure that the **AWS Blu Age** engine is selected\. Then choose **Next**\.  
![\[The AWS Mainframe Modernization application Specify basic information page with the AWS Blu Age engine selected.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/m2-ba-app-basic.png)

1. On the **Specify resources and configurations** page, copy and paste the updated application definition JSON\.  
![\[The AWS Mainframe Modernization Resources and configurations section with the updated application definition JSON pasted in.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/m2-ba-resources-configs.png)

1. In **Review and create**, review your choices, and then choose **Create application**\.

## Step 5: Deploy an application<a name="tutorial-runtime-ba-step5"></a>

After you successfully create both the AWS Mainframe Modernization runtime environment and application, and both are in the **Available** state, you can deploy the application into the runtime environment\. To do this, complete the following steps:

1. Navigate to **AWS Mainframe Modernization** in the AWS Management Console\. In the navigation pane, choose **Environments**\. The Environments list page is displayed\.  
![\[The AWS Mainframe Modernization runtime environments list .\]](http://docs.aws.amazon.com/m2/latest/userguide/images/m2-ba-environments.png)

1. Choose the previously created runtime environment\. The environment details page is displayed\. 

1. Choose **Deploy application**\.   
![\[The AWS Mainframe Modernization environment details page for the planets-demo-env environment.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/m2-ba-env-details-planets.png)

1. Choose the previously created application, then choose **Deploy**\.  
![\[The AWS Mainframe Modernization Deploy application page with the planets demo app displayed.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/m2-ba-app-deploy.png)

1. Wait until the application finishes its deployment\. You'll see a banner with the message **Application was deployed successfully**\.

## Step 6: Start an application<a name="tutorial-runtime-ba-step6"></a>

1. Navigate to **AWS Mainframe Modernization** in the AWS Management Console and choose **Applications**\.

1. Choose your application, and then go to **Deployments**\. The status of the application should be **Succeeded**\.  
![\[The AWS Mainframe Modernization Deployments page showing a deployment status of Succeeded.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/m2-ba-app-deployments-succeeded.png)

1. Choose **Actions**, and then choose **Start application**\.

## Step 7: Access the application<a name="tutorial-runtime-ba-step7"></a>

1. Wait until the application is in the **Running** state\. You'll see a banner with the message **Application was started successfully**\.

1. Copy the application DNS hostname\. You can find this hostname in the **Application information** section of the application\.

1. In a browser, navigate to `http://{hostname}:{portname}/PlanetsDemo-web-1.0.0/`, where:
   + `hostname` is the DNS hostname copied previously\.
   + `portname` is the Tomcat port defined in the application definition you created in [Step 2: Create the application definition](#tutorial-runtime-ba-step2)\.

   The JICS screen appears\.  
![\[The JICS transaction launcher page.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/ba-jics-launcher.png)

If you can't access the application, see [Cannot access an application's URL](both-application-connectivity.md)\.

## Step 8: Test the application<a name="tutorial-runtime-ba-test"></a>

In this step, you run a transaction in the migrated application\.

1. On the JICS screen, enter `PINQ` in the input field, and choose **Run** \(or press Enter\) to start the application transaction\.

   The demo app screen should appear\.  
![\[The PlanetsDemo application screen in insert mode.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/ba-demo-app-screen.png)

1. Type a planet name in the corresponding field and press Enter\.  
![\[The PlanetsDemo application screen with Earth entered in the Planet name field.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/ba-demo-with-data.png)

   You should see details about the planet\.

## Clean up resources<a name="tutorial-runtime-ba-clean"></a>

If you no longer need the resources that you created for this tutorial, delete them to avoid additional charges\. To do so, complete the following steps:
+ If the AWS Mainframe Modernization application is still running, stop it\.
+ Delete the application\. For more information, see [Delete an AWS Mainframe Modernization application](applications-m2-delete.md)\.
+ Delete the runtime environment\. For more information, see [Delete an AWS Mainframe Modernization runtime environmentDelete a runtime environment](delete-environments-m2.md)\.

## Next steps<a name="tutorial-runtime-ba-next"></a>

To learn more, you can explore the following tutorials:
+ [Tutorial: Setting up the build for the Planets demo app](tutorial-build-ba.md)