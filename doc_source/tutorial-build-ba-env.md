# Set up your CI/CD environment<a name="tutorial-build-ba-env"></a>

The steps in this section help you set up a CI/CD pipeline environment for the PlanetsDemo sample application\. We recommend that you complete each of these steps as though you were doing it for the first time\. Even if you have existing resources, such as a CodeArtifact repository, create another one for this tutorial\. You can delete it when you finish the tutorial\. These steps assume that you are connected to an available Amazon EC2 instance and that you can access the AWS Management Console at any time\.

**Topics**
+ [Step 1: Configure Git](#tutorial-build-ba-env-git)
+ [Step 2: Install or upgrade and configure the AWS CLI](#tutorial-build-ba-env-cli)
+ [Step 3: Create a CodeArtifact domain and repository](#tutorial-build-ba-env-aca)
+ [Step 4: Generate HTTPS Git credentials for CodeCommit](#tutorial-build-ba-env-https)
+ [Step 5: Create an CodeCommit repository](#tutorial-build-ba-env-acc)
+ [Step 6: Connect to the repository and clone it](#tutorial-build-ba-env-clone)
+ [Step 7: Update pom\.xml](#tutorial-build-ba-env-pom)
+ [Step 8: Push the code to your CodeCommit repo](#tutorial-build-ba-env-push)
+ [Step 9: Set CodeArtifact environment variables](#tutorial-build-ba-env-envvar)
+ [Step 10: Get a CodeArtifact token](#tutorial-build-ba-env-token)

## Step 1: Configure Git<a name="tutorial-build-ba-env-git"></a>

In this step, you configure Git with your username and email address to identify yourself to Git\. This is a one\-time task\.

1. Run the following command:

   ```
   git config —global user.name your name
   ```

1. Run the following command:

   ```
   git config —global user.emailyou@example.com
   ```

## Step 2: Install or upgrade and configure the AWS CLI<a name="tutorial-build-ba-env-cli"></a>

In this step, you install the AWS Command Line Interface \(AWS CLI\) on your Amazon EC2 instance so that you can call CodeArtifact commands\. This is a one\-time task\. If you have an older version of the AWS CLI installed, you must upgrade it so that the CodeArtifact commands are available\. CodeArtifact commands are available in the following AWS CLI versions:
+ **AWS CLI 1**: 1\.18\.77 and newer
+ **AWS CLI 2**: 2\.0\.21 and newer

To check the version, use the `aws --version` command\.

**To install and configure the AWS CLI**

1. Install or upgrade the AWS CLI\. Follow the instructions in [Installing the AWS Command Line Interface](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html)\.

1. Configure the AWS CLI with the following command: `aws configure`

   When you are prompted, specify your AWS access key and AWS secret access key\. When you are prompted for the default Region name, specify the Region where you will create the build and pipeline, such as `us-east-1`\. When you are prompted for the default output format, specify `json`\.
**Important**  
When you configure the AWS CLI, you are prompted to specify an AWS Region\. Choose one of the supported Regions listed in [AWS Mainframe Modernization endpoints and quotas](https://docs.aws.amazon.com/general/latest/gr/m2.html) in the *Amazon Web Services General Reference*\. All the resources that you create must be in the same AWS account and AWS Region\.

   For more information, see [Configuring the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html) in the *AWS Command Line Interface User Guide* and [Managing access keys for IAM users](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_access-keys.html) in the *IAM User Guide*\.

1. To verify the installation or upgrade, call the following command: `aws m2 help`\.

   If the installation is successful, it displays a list of available AWS Mainframe Modernization commands\.

## Step 3: Create a CodeArtifact domain and repository<a name="tutorial-build-ba-env-aca"></a>

In this step, you create a CodeArtifact repository to contain dependencies that are required by the PlanetsDemo sample application\. In CodeArtifact, repositories are part of domains, so you must also create a domain for your repository\. When you create the repository, you must specify the public Maven upstream connection\. This connection gives you access to the most recent Maven dependencies\. This is a one\-time step\. You complete these steps in the CodeArtifact console\. For more information, see [Getting started with CodeArtifact](https://docs.aws.amazon.com/codeartifact/latest/ug/getting-started.html) in the *CodeArtifact User Guide*\.

1. Open the [CodeArtifact console](https://console.aws.amazon.com/https://console.aws.amazon.com/codesuite/codeartifact/home)\.

1. In the Region selector, choose the AWS Region where you want to create the domain and repository\.

1. Choose **Create repository** and provide the following information\.

   1. In **Repository name**, enter **my\-bluage\-dependency\-repo**\.

   1. \(Optional\) In **Repository description**, enter a description for your repository so that you can identify it as different from any other CodeArtifact repositories\.

   1. In **Public upstream repositories**, choose **maven\-central\-store** to create a repository connected to **maven** that is upstream from your `my-bluage-dependency-repo`\.  
![\[CodeArtifact Create Repository box with name, description, and maven-central-store specified.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/aca-create-repo.png)

   1. Choose **Next**\.

1. In **Domain**, choose **This AWS account**\.

1. In **Domain name** enter **my\-bluage\-domain** and choose **Next**\.

1. In **Review and create**, review what CodeArtifact is creating for you\. When you’re ready, choose **Create repository**\.

## Step 4: Generate HTTPS Git credentials for CodeCommit<a name="tutorial-build-ba-env-https"></a>

In this step, you download specific credentials to configure access to CodeCommit\. You’ll use these credentials when you connect to the CodeCommit repository from your Amazon EC2 instance\.

1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the IAM console navigation pane, choose **Users**, and then choose the user you want to configure for CodeCommit access\.

1. On the user details page, choose the **Security Credentials** tab, and in **HTTPS Git credentials for AWS CodeCommit**, choose **Generate credentials**\.  
![\[IAM generated credentials confirmation.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/iam-generate-credentials.png)

1. Copy the credentials that IAM generated for you\. You can show, copy, and paste this information into a secure file on your local computer, or you can choose **Download credentials** to download this information as a \.CSV file\. You need this information to connect to CodeCommit\.  
![\[IAM downloaded credentials.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/iam-download-credentials.png)

## Step 5: Create an CodeCommit repository<a name="tutorial-build-ba-env-acc"></a>

In this step, you create an empty repository in CodeCommit\. This repository will hold the source code for the PlanetsDemo sample application, that you will upload in a later step\.

1. Sign in to the AWS Management Console and open the CodeCommit console at [https://console\.aws\.amazon\.com/codecommit/](https://console.aws.amazon.com/codecommit/)\.

1. In the Region selector, choose the AWS Region where you want to create the repository\. Make sure it is the same Region as the one where you created the CodeArtifact domain and repository\.

1. On the **Repositories** page, choose **Create repository**\.

1. On the **Create repository** page, in **Repository name**, enter **my\-planetsdemo\-repo** or another name, if that one is unavailable\.
**Note**  
Repository names are case sensitive\. The name must be unique in the AWS Region for your AWS account\.

1. \(Optional\) In **Description**, enter a description for the repository\. This can help you and other users identify the purpose of the repository\.  
![\[CodeCommit Create Repository box with name and description specified.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/acc-create-repo.png)

1. Choose **Create**\.

## Step 6: Connect to the repository and clone it<a name="tutorial-build-ba-env-clone"></a>

In this step, you copy the HTTPS URL for the repository and then clone it on your Amazon EC2 instance\. Although the repository is currently empty, cloning it to your instance now makes it possible for you to upload all the files for the PlanetsDemo sample in bulk, rather than uploading them one by one through the CodeCommit console\.

1. On the details page for the repository, choose **Clone URL** and then choose **Clone HTTPS**\.  
![\[CodeCommit repository details with Clone URL menu displayed and Clone HTTPS selected.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/acc-clone-url.png)

1. Connect to your Amazon EC2 instance and enter `git clone`\. Then paste in the HTTPS URL that you copied\.

1. Enter the HTTPS Git credentials that you generated previously\.

You can disregard the warning message about cloning an empty repository\. You’ll add code to the repository in a later step\.

## Step 7: Update pom\.xml<a name="tutorial-build-ba-env-pom"></a>

In this step, you update the `gapwalk.version` property in the `pom.xml` file of the application to match the gapwalk distribution that you downloaded earlier\. This step is required for the application to run\.

1. On your Amazon EC2 instance, locate and open `pom.xml` with the text editor of your choice\. The file is in the extracted `PlanetsDemo-pom` folder\.

1. Find the `<properties>` section of the file\. It looks something like the following:

   ```
   <properties>
       <spring.boot.version>2.5.12</spring.boot.version>
       <gapwalk.version>3.1.0-SNAPSHOT</gapwalk.version>
       <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
   </properties>
   ```

1. Change `gapwalk.version` to match the current version `3.1.0-b3257-SNAPSHOT`\.
**Note**  
The value that you use for the `gapwalk.version` property must point to an existing gapwalk distribution version\.

## Step 8: Push the code to your CodeCommit repo<a name="tutorial-build-ba-env-push"></a>

In this step, you push the sample source code for PlanetsDemo to the CodeCommit repository that you created previously\.

1. On your Amazon EC2 instance, copy the `PlanetsDemoRepo` code to your cloned CodeCommit repository\.

1. Use Git to push the code to the CodeCommit repository\.  
![\[CodeCommit PlanetsDemo repository showing the sample code folders and files.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/acc-populated-repo.png)

## Step 9: Set CodeArtifact environment variables<a name="tutorial-build-ba-env-envvar"></a>

In this step, you export variables for your CodeArtifact domain and repository\. You need these variables in a later step when you get a CodeArtifact token that you need for authorization\.
+ On your Amazon EC2 instance, run the following commands:

  ```
  export CODEARTIFACT_DOMAIN=my-blueage-domain
  export CODEARTIFACT_REPO=my-bluage-dependency-repo
  ```

## Step 10: Get a CodeArtifact token<a name="tutorial-build-ba-env-token"></a>

In this step, you get a CodeArtifact token\. CodeArtifact uses this token to authenticate and authorize requests from Maven\.

1. On your Amazon EC2 instance, run the following command:

   ```
   export CODEARTIFACT_TOKEN=`aws codeartifact get-authorization-token --domain $CODEARTIFACT_DOMAIN --query authorizationToken --output text`
   ```

1. Run the following command to verify the CodeArtifact token:

   ```
   echo $CODEARTIFACT_TOKEN
   ```

**Note**  
When the token expires, you must refresh it with the previous command\.