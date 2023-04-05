# Tutorial: Blu Age Application Security in AWS Mainframe Modernization<a name="security-app-ba"></a>

This tutorial documents AWS Mainframe Modernization application authentication through Amazon Cognito\.

## Amazon Cognito user pools<a name="user-pools-security-app-ba"></a>

AWS Mainframe Modernization\-deployed Blu Age applications can be configured to use Amazon Cognito user pools as an identity provider\. Amazon Cognito will then store users and their attributes and handle their login process\. Password policies, Multi Factor Authentication and customizable email/phone number messages are available\. Blu Age applications security is based on SpringSecurity; its OAuth2 features are used to interface with Amazon Cognito\. For more information on Amazon Cognito user pools, see [Amazon Cognito user pools](https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-user-identity-pools.html) in the *Amazon Cognito Developer Guide*\.

## Third\-party identity providers<a name="third-party-security-app-ba"></a>

Amazon Cognito can delegate authentication to an existing, third party identity provider through the Federated Identity Providers feature\. This article [Role\-based access control using Amazon Cognito and an external identity provider](http://aws.amazon.com/blogs/fr/blogs/security/role-based-access-control-using-amazon-cognito-and-an-external-identity-provider/) provides an example of such setup\.

## Example setup<a name="example-security-app-ba"></a>

**Topics**
+ [Demo Resources](#example-security-app-ba-demo)
+ [Domain registration](#example-security-app-ba-domain)
+ [TLS certificate emission](#example-security-app-ba-tls)
+ [Amazon Cognito Setup](#example-security-app-ba-cog-setup)
+ [Secret Manager](#example-security-app-ba-asm)
+ [User Creation](#example-security-app-ba-cog-user)
+ [Demo App Upload](#example-security-app-ba-s3-upload)
+ [Application Resources Creation](#example-security-app-ba-app-resources)
+ [Application Definition Adjustments](#example-security-app-ba-app-def-adjust)
+ [AWS Mainframe Modernization Environment Creation](#example-security-app-ba-app-m2-env)
+ [AWS Mainframe Modernization Application Creation](#example-security-app-ba-app-m2-app)
+ [AWS Mainframe Modernization Application Deployment](#example-security-app-ba-app-m2-deploy)
+ [AWS Mainframe Modernization Application Startup](#example-security-app-ba-app-m2-start)
+ [Accessing the Application](#example-security-app-ba-app-m2-access)

The following sections will document the deployment of an AWS Mainframe Modernization Blu Age application delegating its authentication to Amazon Cognito\.

### Demo Resources<a name="example-security-app-ba-demo"></a>

This demo application archive will be needed in the following steps: [PlanetsDemo\-v1\.zip](https://d3lkpej5ajcpac.cloudfront.net/demo/bluage/PlanetsDemo-v1.zip)\. 

### Domain registration<a name="example-security-app-ba-domain"></a>

Amazon Cognito requires the integrating \(“client”\) application to be exposed through an HTTPS\-secure URL\. For this purpose, we will need a domain name, and a TLS certificate for this domain\.

The combination of Route 53 and AWS Certificate Manager provides us with an integrated solution for this task\.

First navigate to the Route 53 console\. There, choose **Registered domains**, then choose **Register Domain**\.

Use **Choose a domain name** to pick an available one\. On the following page, enter your contact details then choose **Add to Cart**\. Do a final review of your choices, then choose **Complete Order**\.

You may need to answer a validation email, then wait for the domain registration to take place\.

### TLS certificate emission<a name="example-security-app-ba-tls"></a>

In this step, you request a certificate for the domain you registered previously\.

1. When the previously created domain name is available, navigate to ACM in the AWS Management Console\.

1. Choose **Request a certificate**, and in **Certificate type**, choose **Request a public certificate**\.  
![\[The Request certificate page with Request a public certificate selected.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/acm-request-cert.png)

1. Choose **Next**\.

1. On **Request public certificate**, enter the domain name you previously registered and leave **DNS validation** selected\.  
![\[The Request public certificate page with the domain name entered and DNS validation selected.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/acm-request-pub-cert.png)

   You should then get a `Successfully requested certificate` message\.

1. Choose **View Certificate**, and notice that its current status is **Pending validation**\. Since it relates to a Route 53 domain, you can use the console to create the necessary DNS\-validation records\. For more information, see [Validating domain ownership](https://docs.aws.amazon.com/acm/latest/userguide/domain-ownership-validation.html) in the *AWS Certificate Manager User Guide*\. For this, choose **Create records in Route 53** in **Domains**, then choose **Create records**\.  
![\[The Create DNS records in Amazon Route 53 page with the pending certificate selected.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/acm-create-dns-rec.png)

   You should get a `Successfully created DNS records` message\. Wait until the certificate gets in the **Issue** state\.

### Amazon Cognito Setup<a name="example-security-app-ba-cog-setup"></a>

For testing purposes we will create a user pool managing local user profiles\.

1. Navigate to Amazon Cognito in the AWS Management Console\. Choose **User pools**, then **Create user pool**\.  
![\[The Amazon Cognito user pools page.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/cog-user-pool-create.png)

1. In **Configure sign\-in experience**, keep the **Cognito user pool** default provider type\. You can choose one or multiple **Cognito user pool sign\-in options**; for now, choose **User name**, then choose **Next**\.  
![\[The Cognito authoentication provider page with Cognito user pool and user name selected.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/cog-auth-provider.png)

1. In **Configure security requirements**, either keep the defaults or adjust passwords and login requirements as appropriate\. Choose **Next**\.

1. In **Configure sign\-up experience**, since this is a demo app, disable **Enable self\-registration** as a security measure and choose **Next**\.  
![\[The Cognito self-service sign-up page with enable self-registration disabled.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/cog-config-sign-up.png)

1. In **Configure message delivery**, you can use **Send email with Amazon SES** if you already have an Amazon SES identity existing in your account\. Otherwise choose **Send email with Cognito** for testing purposes\. You can add a valid **REPLY\-TO email address** if you like\. Choose **Next**\.  
![\[The Cognito email page with Send email with Cognito selected.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/cog-email.png)

1. In **Integrate your app**, choose a name for your user pool\. In **Hosted authentication pages**, choose **Use the Cognito Hosted UI**:  
![\[The Cognito hosted authentication pages section with Use the Cognito hosted UI selected.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/cog-hosted-auth.png)

1. For simplicity’s sake, in **Domain**, choose **Use a Cognito domain** and enter a domain prefix; for example, `https://planetsdemo`\.  
![\[The Cognito domain page with use a Cognito domain selected and planetsdemo entered for the domain prefix.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/cog-domain.png)

1. The demo app must be added as a client\. In **Initial app client**, choose **Confidential client**\. Enter an app client name, such as `planetsdemo` and choose **Generate a client secret**:  
![\[The Cognito initial app client page with Confidential client selected and planetsdemo entered for the app client name. Generate a client secret is also selected.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/cog-initial-app-client.png)

1. In **Allowed callback URL** use an URL with the following canonical format:

   ```
   https://{m2-address}/{app-name}:{app-port}/login/oauth2/code/cognito
   ```

   Where:
   + `m2-address` is the DNS name of the AWS Mainframe Modernization entry point \(use the domain specified in the certificate registered earlier\)\.
   + `app-name` is the name of the application deployed on AWS Mainframe Modernization \(`*-web` or `*-service` typically\)\.
   + `app-port` is the \(tomcat\) port where this application is deployed \(as specified below in the application definition\)\.

   You can also use a temporary `http://localhost` URL, then edit it later\.

   Keep default values in the **Advanced app client settings** and **Attribute read and write permissions** sections, then choose **Next**\.

1. In **Review and create**, verify your choices then choose **Create user pool**\.

### Secret Manager<a name="example-security-app-ba-asm"></a>

A secret manager is needed to host the client secret generated by Amazon Cognito during the previous step\. For this purpose use AWS Secrets Manager\.

1. Navigate to Secrets Manager in the AWS Management Console\.

1. Choose **Store a new secret**\.

1. In **Secret type**, choose **Other type of secret**, and enter `client-secret` for the key\. For the value, copy and paste the client secret generated by Amazon Cognito\. You can find this value at **Cognito** > **User pool** > **App client** > **Show client secret\.** Choose **Next**\.  
![\[The Secrets Manager store a new secret page, with other tpe of secret selected and client-secret entered in the key field.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/asm-store-new-secret.png)

1. In **Secret name and description**, give the secret a name, then choose **Next**\.

1. In **Secret rotation**, leave **Automatic rotation** disabled\. Choose **Next**\.

1. In **Review**, look over the information you provided, then choose **Store**\.

1. After the secret is created, you must enable access to it from AWS Mainframe Modernization\. To do this, choose the secret, then choose **Edit permissions**:  
![\[The Secrets Manager resource permissions page with edit permissions selected.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/asm-resource-edit-perm.png)

1. In the displayed JSON editor, paste the following policy and choose **Save**:

   ```
   {
       "Version" : "2012-10-17",
       "Statement": [
           {
               "Effect" : "Allow",
               "Principal" : {
                 "Service" : "m2.amazonaws.com"
               },
               "Action" : "secretsmanager:GetSecretValue",
               "Resource" : "*"
           }
       ]
   }
   ```

### User Creation<a name="example-security-app-ba-cog-user"></a>

Since self\-registration is disabled, create a Amazon Cognito user\.

1. Navigate to Amazon Cognito in the AWS Management Console\.

1. Choose the user pool you created, then in **Users** choose **Create user\.** 

1. In **User information**, choose **Send an email invitation**, enter a user name and an email address, and choose **Generate a password\.** Choose **Create user**\.  
![\[The Amazon Cognito user information page with send an email invitation selected.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/cog-user-info.png)

### Demo App Upload<a name="example-security-app-ba-s3-upload"></a>

Upload the demo application to an Amazon S3 bucket; here, a bucket named `planetsdemo` will be used, with a key prefix \(folder\) of name `v1` and an archive name of `planetsdemo-v1.zip`\.

### Application Resources Creation<a name="example-security-app-ba-app-resources"></a>

This demo application does not need extra AWS resources\.

### Application Definition Adjustments<a name="example-security-app-ba-app-def-adjust"></a>

The following is an AWS Mainframe Modernization application definition for the demo application:

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
    },
    {
      "resource-type": "cognito-external",
      "resource-id": "cognito-client",
      "properties": {
        "issuer-uri": "https://cognito-idp.{REGION_ID}.amazonaws.com/{POOL_ID}",
        "domain-name": "{DOMAIN_NAME}",
        "client-id": "{CLIENT_ID}",
        "secret-manager-arn": "{SECRET_MANAGER_ARN}"
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

Replace the following placeholders as described; needed information can be found on the **App integration** tab of your previously created Amazon Cognito user pool:
+ `{REGION_ID}`: the region where the application is to be deployed\.
+ `{POOL_ID}`: the Amazon Cognito Pool ID\.
+ `{DOMAIN_NAME}`: the full URL of the Amazon Cognito domain created during the “integrate your app” step\.
+ `{CLIENT_ID}`: the **Client ID** value created for your application by Amazon Cognito\.
+ `{SECRET_MANAGER_ARN}`: the ARN of the previously created secret in Secrets Manager\.

Adjust the `ba-application` and `s3-source` entries as needed to point to the Amazon S3 bucket where the application was uploaded\.

### AWS Mainframe Modernization Environment Creation<a name="example-security-app-ba-app-m2-env"></a>

Create the AWS Mainframe Modernization runtime environment by following these steps:

1. Navigate to AWS Mainframe Modernization in the AWS Management Console\.

1. Choose **Environments**, the choose **Create environment**\.

1. In **Specify basic information**, enter a name and description, then choose the **AWS Blu Age** engine\.

1. In **Specify configurations**:
   + Choose **Standalone runtime environment**\.
   + Choose **Allow applications deployed to this environment to be publicly accessible**\.
   + Choose two subnets\.

1. In **Attach storage \- Optional**, leave the default values\.

1. In **Review and create**, review the information, then choose **Create environment**\.

### AWS Mainframe Modernization Application Creation<a name="example-security-app-ba-app-m2-app"></a>

1. Navigate to AWS Mainframe Modernization in the AWS Management Console\.

1. Choose **Applications**, then choose **Create application**\.

1. In **Specify basic information**, enter a name and description, then choose the **AWS Blu Age** engine\.

1. In **Specify resources and configurations**, copy and paste the previously updated Application Definition JSON\.

1. In **Review and create**, review the information, then choose **Create application**\.

### AWS Mainframe Modernization Application Deployment<a name="example-security-app-ba-app-m2-deploy"></a>

After both the AWS Mainframe Modernization environment and application are created successfully and are both in the **Available** state:

1. Navigate to AWS Mainframe Modernization in the AWS Management Console\.

1. Choose **Environments**, then choose the previously created environment\.

1. Choose **Deploy application**\.

1. Choose the previously created application\.

1. Choose **Deploy**\.

### AWS Mainframe Modernization Application Startup<a name="example-security-app-ba-app-m2-start"></a>

1. Navigate to AWS Mainframe Modernization in the AWS Management Console\.

1. Choose **Applications**\.

1. Choose your application, then scroll to **Deployments**: it should be in the **Ready** state\.

1. Choose **Actions**, then choose **Start application**\.

### Accessing the Application<a name="example-security-app-ba-app-m2-access"></a>

1. Wait until the application is in the **Running** state\.

1. Copy the application DNS hostname\.

1. In a browser, navigate to `https://{hostname}:{portname}/PlanetsDemo-web-1.0.0/`\.

## Clean up resources<a name="security-app-ba-clean"></a>

If you no longer need the resources you created for this tutorial, delete them so that you won't continue to be charged for them\. Complete the following steps:
+ If the AWS Mainframe Modernization application is still running, stop it\.
+ Delete the AWS Mainframe Modernization application\. For more information, see [Delete an AWS Mainframe Modernization application](applications-m2-delete.md)\.
+ Delete the AWS Mainframe Modernization runtime environment\. For more information, see [Delete an AWS Mainframe Modernization runtime environmentDelete a runtime environment](delete-environments-m2.md)\.
+ Delete the Amazon Cognito resources you created\. For more information, see [Tutorial: Cleaning up your AWS resources](https://docs.aws.amazon.com/cognito/latest/developerguide/tutorial-cleanup-tutorial.html) in the *Amazon Cognito Developer Guide*\.
+ Delete the Secrets Manager secret you created\. For more information, see [Delete a secret](https://docs.aws.amazon.com/secretsmanager/latest/userguide/manage_delete-secret.html) in the *AWS Secrets Manager User Guide*\.