# Set up your CI/CD build and pipeline<a name="tutorial-build-ba-cicd"></a>

The steps in this section help you set up the PlanetsDemo sample application build and a pipeline for the build\. These are steps that you will complete each time that you want to set up a CI/CD pipeline for one of your migrated applications\. We recommend that you complete each of these steps as though you were doing it for the first time\. Even if you ran the scripts previously, use different names or targets so that you can run them again and not overwrite anything\. These steps assume that you are connected to an available Amazon EC2 instance and that you can access the AWS Management Console at any time\.

**Topics**
+ [Step 1: Generate a Maven configuration file](#tutorial-build-ba-cicd-generate)
+ [Step 2: Copy the Maven configuration file to the gapwalk directory](#tutorial-build-ba-cicd-copy)
+ [Step 3: Deploy Blu Age dependencies into the CodeArtifact repository](#tutorial-build-ba-cicd-deploy)
+ [Step 4: Edit the build parameters file](#tutorial-build-ba-cicd-build-parms)
+ [Step 5: Create and deploy the CodeBuild project](#tutorial-build-ba-cicd-build-proj)
+ [Step 6: Start a build](#tutorial-build-ba-cicd-build-start)
+ [Step 7: View build information](#tutorial-build-ba-cicd-build-verify)
+ [Step 9: Specify the default branch of the source code repository](#tutorial-build-ba-cicd-pipeline-branch)
+ [Step 10: Edit the pipeline parameters file](#tutorial-build-ba-cicd-pipeline-parms)
+ [Step 11: Create and deploy the CodePipeline pipeline](#tutorial-build-ba-cicd-pipeline-deploy)
+ [Step 12: View the pipeline](#tutorial-build-ba-cicd-pipeline-test)

## Step 1: Generate a Maven configuration file<a name="tutorial-build-ba-cicd-generate"></a>

In this step, you run the provided `generate-maven-config.sh` script to generate a `settings.xml` file for Maven\. The script is provided in the `build-pipeline.zip` archive, that you downloaded and extracted previously\.

1. Locate the `generate-maven-config.sh` script\.

1. \(Optional\) If you use named profiles with the AWS CLI, edit the script and append the `--profile your-profile-name` option to ensure that the script can access the correct environment and generate a valid configuration file\.

1. Run the script with the command `./generate-maven-config.sh`

1. Open the `settings.xml` file that the script generates to see how the variables you exported are used\.

   ```
   <settings>
       <servers>
           <server>
               <id>codeartifact</id>
               <username>aws</username>
               <password>${env.CODEARTIFACT_TOKEN}</password>
           </server>
       </servers>
       <mirrors>
           <mirror>
               <id>codeartifact</id>
               <name>codeartifact</name>
               <url>https://my-bluage-domain-111122223333.d.codeartifact.us-east-1.amazonaws.com/maven/my-bluage-dependency-repo/</url>
               <mirrorOf>*</mirrorOf>
            </mirror>
       </mirrors>
       <profiles>
           <profile>
               <id>codeartifact</id>
               <activation>
                   <activeByDefault>true</activeByDefault>
               </activation>
               <repositories>
                   <repository>
                       <id>codeartifact</id>
                       <url>https://my-bluage-domain-111122223333.d.codeartifact.us-east-1.amazonaws.com/maven/my-bluage-dependency-repo/</url>
                   </repository>
               </repositories>
           </profile>
       </profiles>
   </settings>
   ```

## Step 2: Copy the Maven configuration file to the gapwalk directory<a name="tutorial-build-ba-cicd-copy"></a>

In this step, you make sure that the Maven configuration file that you created previously occupies the same directory as the `gapwalk` JAR files\.

1. Locate the `settings.xml` file that the `generate-maven-config.sh` script created in the previous step\.

1. Copy the file to the same directory as the `gapwalk` JAR files\.

   ```
   cp settings.xml directory-with-extracted-assets/
   ```

## Step 3: Deploy Blu Age dependencies into the CodeArtifact repository<a name="tutorial-build-ba-cicd-deploy"></a>

In this step, you deploy the Blu Age dependencies into CodeArtifact so that they are available for the build that you will set up later\.

1. Locate the `deploy-to-codeartifact.sh` script\.

1. Run the following command: `./deploy-to-codeartifact.sh`

## Step 4: Edit the build parameters file<a name="tutorial-build-ba-cicd-build-parms"></a>

In this tutorial, you create a CodeBuild project with AWS CloudFormation\. This service creates AWS resources for you based on information that you provide\. We’ve provided an AWS CloudFormation template that you can run from the command line on your Amazon EC2 instance\. That template creates an CodeBuild project\. In this step, you’ll edit a JSON file that contains some parameters required by the AWS CloudFormation template\.

1. On your Amazon EC2 instance, locate the `build-project-params.json` file\. It looks like the following:

   ```
   {
       "Parameters": {
           "CodeRepositoryName": "",
           "ArtifactRepositoryName": "",
           "ArtifactRepositoryDomain": ""
       }
   }
   ```

1. Edit the file as follows:
   + For `CodeRepositoryName`, enter the name of the CodeCommit repository that you created for this tutorial, for example, `PlanetsDemoRepo`\.
   + For `ArtifactRepositoryName`, enter the name of the CodeArtifact repository that you created for this tutorial, for example, `my-bluage-dependency-repo`\.
   + For `ArtifactRepositoryDomain`, enter the name of the CodeArtifact domain where you located the CodeArtifact repository that you created, for example, `my-bluage-domain`\.

   Your updated file should look something like this:

   ```
   {
       "Parameters": {
           "CodeRepositoryName": "PlanetsDemoRepo",
           "ArtifactRepositoryName": "my-bluage-dependency-repo",
           "ArtifactRepositoryDomain": "my-bluage-domain"
       }
   }
   ```

## Step 5: Create and deploy the CodeBuild project<a name="tutorial-build-ba-cicd-build-proj"></a>

In this step, you use AWS CloudFormation to create and deploy a CodeBuild project\. That project uses the parameters that you supplied in the previous step to identify the location of the source code and the project dependencies\. You call AWS CloudFormation from your Amazon EC2 instance with the AWS CLI\.
+ Run the following command:

  ```
  aws cloudformation deploy --stack-name your-stack-name --template-file build-project.yaml --capabilities CAPABILITY_IAM --parameter-overrides file://build-project-params.json
  ```

  It takes a few minutes for AWS CloudFormation to finish creating the resources\. You can watch the progress in the AWS CloudFormation console\.

## Step 6: Start a build<a name="tutorial-build-ba-cicd-build-start"></a>

In this step, you use CodeBuild to run a build with the build project that AWS CloudFormation created for you in the previous step\.

1. Sign in to the AWS Management Console and open the CodeBuild console at [https://console\.aws\.amazon\.com/codebuild/](https://console.aws.amazon.com/codebuild/)\.

1. In the navigation pane, choose **Build projects**\.  
![\[The created AWS Mainframe Modernization build project in the CodeBuild list of build projects.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/acb-start-build.png)

1. In the list of build projects, choose the project that starts with `M2Build`, and then choose **Start build**\. The build starts immediately\.

Wait for the build to finish\.

## Step 7: View build information<a name="tutorial-build-ba-cicd-build-verify"></a>

In this step, you verify the build status and output artifacts by viewing the build information\.

1. In the CodeBuild console, if you're not already viewing the details of the build that you started in the previous step, choose **Build history**\. Then choose the **Build run** link for the build project that starts with `M2Build`\.

1. On the **Build Status** page, in **Phase details**, make sure that each of the build phases shows **Succeeded** in the **Status** column:  
![\[The CodeBuild phase details with all build stages showing success\]](http://docs.aws.amazon.com/m2/latest/userguide/images/acb-stages-success.png)

1. With the CodeBuild console still open and the build details page still displayed from the previous step, choose **Build details** and scroll down to the **Artifacts** section\.

   The link to the Amazon S3 folder that contains the build output artifact appears after **Artifacts upload location**\. This link opens the folder in Amazon S3 where you find the build output artifact file\.  
![\[Location of the link to the build output artifact in the CodeBuild console.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/acb-output-artifact-link.png)

## Step 9: Specify the default branch of the source code repository<a name="tutorial-build-ba-cicd-pipeline-branch"></a>

In this step, you edit the file that will create the CodePipeline pipeline for you with AWS CloudFormation\. You must customize the default branch of the code repository specified in the file\.

1. On your Amazon EC2 instance, edit the `build-pipeline.yaml` file\.

1. Locate the `CodeRepositoryBranch` parameter\.

   ```
   Parameters:
     CodeRepositoryBranch:
       Type: String
       Default: main
   ```

1. Change `main` to `master`\.

## Step 10: Edit the pipeline parameters file<a name="tutorial-build-ba-cicd-pipeline-parms"></a>

Similar to how you created the CodeBuild project in a previous step, you’ll create the CodePipeline pipeline with AWS CloudFormation\. We’ve provided an AWS CloudFormation template that you'll run from the command line on your Amazon EC2 instance\. That template creates a CodePipeline pipeline\. In this step, you’ll edit a JSON file that contains some parameters required by the AWS CloudFormation template\.

1. On your Amazon EC2 instance, locate the `build-pipeline-params.json` file\. It looks like the following listing:

   ```
   {
       "Parameters": {
           "CodeRepositoryName": "",
           "ArtifactRepositoryName": "",
           "ArtifactRepositoryDomain": ""
       }
   }
   ```

1. Edit the file as follows:
   + For `CodeRepositoryName`, enter the name of the CodeCommit repository that you created for this tutorial, for example, `PlanetsDemoRepo`\.
   + For `ArtifactRepositoryName`, enter the name of the CodeArtifact repository that you created for this tutorial, for example, `my-bluage-dependency-repo`\.
   + For `ArtifactRepositoryDomain`, enter the name of the CodeArtifact domain where you located the CodeArtifact repository that you created, for example, `my-bluage-domain`\.

   Your updated file should look something like this:

   ```
   {
       "Parameters": {
           "CodeRepositoryName": "PlanetsDemoRepo",
           "ArtifactRepositoryName": "my-bluage-dependency-repo",
           "ArtifactRepositoryDomain": "my-bluage-domain"
       }
   }
   ```

## Step 11: Create and deploy the CodePipeline pipeline<a name="tutorial-build-ba-cicd-pipeline-deploy"></a>

In this step, you use AWS CloudFormation to create and deploy a CodePipeline pipeline\. That pipeline will use the parameters that you supplied in the previous step to identify the location of the source code and the project dependencies\. You call AWS CloudFormation from your Amazon EC2 instance with the AWS CLI\.
+ Run the following command:

  ```
  aws cloudformation deploy --stack-name your-stack-name --template-file build-pipeline.yaml --capabilities CAPABILITY_IAM --parameter-overrides file://build-pipeline-params.json 
  ```

It takes a few minutes for AWS CloudFormation to finish creating the pipeline\. You can watch the progress in the AWS CloudFormation console\.

## Step 12: View the pipeline<a name="tutorial-build-ba-cicd-pipeline-test"></a>

In this step, you view the pipeline that AWS CloudFormation created for you to make sure that it is working\. This pipeline downloads the source code from the CodeCommit repository\. It then builds the project, including installing all dependencies to your CodeArtifact repository, and deploys the project to an Amazon S3 bucket\.

1. Sign in to the AWS Management Console and open the CodePipeline console at [https://console\.aws\.amazon\.com/codepipeline/](https://console.aws.amazon.com/codepipeline/)\.

1. Under **Name** choose the pipeline that was created for you\.

1. On the details page, review the **Source**, **Build**, and **DeployStaging** stages\.  
![\[The CodePipeline console showing success for each pipeline stage.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/acp-pipeline-success.png)

This pipeline starts automatically when you make changes to the source code and then push the changes to your CodeCommit repository\.