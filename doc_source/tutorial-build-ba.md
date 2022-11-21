# Tutorial: Setting up the build for the Planets demo app<a name="tutorial-build-ba"></a>

This guide explains how to build the Planets demo app using AWS CodeBuild\.

**Topics**
+ [Prerequisites](#tutorial-build-ba-prerequisites)
+ [Overview of steps to perform](#tutorial-build-ba-overview)
+ [Step 1: Push the source code to the repo](#tutorial-build-ba-step1)
+ [Step 2: Generate the Maven config](#tutorial-build-ba-step2)
+ [Step 3: Deploy Blu Age dependencies into the CodeArtifact repository](#tutorial-build-ba-step3)
+ [Step 4: Deploy the CodeBuild project](#tutorial-build-ba-step4)
+ [Overview of CodeBuild process](#tutorial-build-ba-acb-details)
+ [Clean up resources](#tutorial-build-ba-clean)

## Prerequisites<a name="tutorial-build-ba-prerequisites"></a>

The following resources must exist in the target AWS account and AWS Region:
+ An AWS CodeArtifact repository \(and a corresponding domain\)\. You will need to create this repo\. For this demo, specify the public `maven` upstream connection when creating the repo\. For more information, see [Getting started with CodeArtifact](https://docs.aws.amazon.com/codeartifact/latest/ug/getting-started.html) in the *CodeArtifact User Guide*\.
+ An AWS CodeCommit repository as source for the pipeline\. You will need to create this repo\. For more information, see [Getting started](https://docs.aws.amazon.com/codecommit/latest/userguide/getting-started-topnode.html) in the *AWS CodeCommit User Guide*\.

## Overview of steps to perform<a name="tutorial-build-ba-overview"></a>

These steps will be further explained in the following sections\.

**Note**  
You can skip the Maven and the CodeArtifact steps if you already have a CodeArtifact repo with all the dependencies\.

1. Push the code to the CodeCommit repo\.

1. Generate the Maven config for pushing Blu Age dependencies to a CodeArtifact repo\.

1. Push the Blu Age dependencies to the CodeArtifact repo\.

1. Deploy the CodeBuild project\.

## Step 1: Push the source code to the repo<a name="tutorial-build-ba-step1"></a>

1. Download and unzip the [PlanetsDemo\-pom\.zip](https://d3lkpej5ajcpac.cloudfront.net/appstream/bluage/developer-ide/PlanetsDemo/PlanetsDemo-pom.zip) archive file\.

1. Copy the source code from the `BluAge/planets-demo/PlanetsDemo-pom` directory of this repo to a new non\-versioned directory\.

1. Download the [gapwalk distribution](https://d3lkpej5ajcpac.cloudfront.net/library/bluage/gapwalk-3.1.0-b3257-SNAPSHOT-stub.zip)

1. Download the `gapwalk` distribution\. Make sure the `<gapwalk.version>` property in the root `pom.xml` file is set to a correct version \(edit if needed\)\.

   This version must point to an existing `gapwalk` distribution version; for example, currently the version `3.1.0-b3257-SNAPSHOT` is provided\.

1. Commit the code to the `main` branch and push the branch to the CodeCommit repo\.

## Step 2: Generate the Maven config<a name="tutorial-build-ba-step2"></a>

1. Prepare your shell environment variables for the scripts \(replace `{your-domain-name}` and `{your-codeartifact-repo-name}` accordingly\):

   ```
   export CODEARTIFACT_DOMAIN={your-domain-name}
   export CODEARTIFACT_REPO={your-codeartifact-repo-name}
   ```

1. Make sure your AWS environment is configured with the correct AWS account and AWS Region, where the CodeArtifact repo is located\.

1. Get the authorization token for your CodeArtifact repo\. This token will be used by the config file generated below\. You will need to refresh this token from time to time by running this command, when the token expires\.

   ```
   export CODEARTIFACT_TOKEN=`aws codeartifact get-authorization-token --domain ${CODEARTIFACT_DOMAIN} --query authorizationToken --output text`
   ```

1. Run the provided `generate-maven-config.sh` file to get a `settings.xml` for Maven

   ```
   ./generate-maven-config.sh
   ```

## Step 3: Deploy Blu Age dependencies into the CodeArtifact repository<a name="tutorial-build-ba-step3"></a>

1. Download the [gapwalk](https://d3lkpej5ajcpac.cloudfront.net/library/bluage/gapwalk-3.1.0-b3257-SNAPSHOT-stub.zip) distribution\.

1. Extract the archive contents\.

1. Copy the `settings.xml` file to the directory where the `gapwalk` jar files are located\.

   ```
   cp settings.xml {dir-with-extracted-assets/}
   ```

1. In the directory with the extracted files, run the provided script `deploy-to-codeartifact.sh`\.

   ```
   cd {dir-with-extracted-assets}
   path-to-script/deploy-to-codeartifact.sh
   ```

## Step 4: Deploy the CodeBuild project<a name="tutorial-build-ba-step4"></a>

These steps describe a stand alone CodeBuild project to build the artifacts\. You can integrate this CodeBuild project into your setup as required\.

1. Update the parameter file `build-project-params.json` with the corresponding values for `CodeRepositoryName`, `ArtifactRepositoryName`, and `ArtifactRepositoryDomain`\.

1. Deploy the stack `build-project.yaml`\.

   ```
   aws cloudformation deploy --stack-name {your-stack-name} --template-file build-project.yaml \
        --capabilities CAPABILITY_IAM --parameter-overrides file://build-project-params.json
   ```

Now, a CodeBuild project should be created and connected to the CodeCommit repository\. To get the name of the created project, use the AWS console and check the **Outputs** tab of the created stack, or use this CLI command:

```
aws cloudformation describe-stacks --stack-name {your-stack-name} \
    --query "Stacks[0].Outputs[?OutputKey=='CodeBuildProject'].OutputValue" --output text
```

You can start a build using AWS console or AWS CLI and check for errors\. For example, you can start the build using the CLI like this:

```
aws codebuild start-build --project-name {name-of-the-codebuild-project}
```

**To view the progress of your build**

1. On the CodeBuild console, choose **Build History**\.

1. Choose **Build Run**, and then choose **Build Details**\.

1. Scroll down to review the Buildspec file\.

## Overview of CodeBuild process<a name="tutorial-build-ba-acb-details"></a>

The CodeBuild step needs a Maven repository endpoint to execute a Maven build\. To obtain this endpoint, use `aws codeartifact get-repository-endpoint` in the `pre_build` section of the Buildspec file\.

```
aws codeartifact get-repository-endpoint --domain ${CODEARTIFACT_DOMAIN} \
    --repository ${CODEARTIFACT_REPO} --format maven --output text
```

**Important**  
The repository password is represented by a token\. For security reasons, do not store the password as plain text in the file\. Instead, use the `$CODEARTIFACT_TOKEN` environment variable in the `pre-build` section of the Buildspec file\. You can obtain the password by calling `aws codeartifact get-authorization-token`\. 

```
aws codeartifact get-authorization-token --domain ${CODEARTIFACT_DOMAIN} \
    --query authorizationToken --output text
```

To let Maven know to use a specific Maven repo, create an additional file called `settings.xml` and put the endpoing for the Maven repo into this file\. The endpoint is stored in the `$MAVEN_REPO_URL` variable\. The `pre-build` script substitutes the value when it writes the file\. To provide the settings file to Maven, use the `-s` option on the `mvn` command in the `build` section of the Buildspec file\.

```
mvn -s settings.xml package
```

The generated `settings.xml` file will look like the following:

```
   <settings>
    <servers>
        <server>
            <id>codeartifact</id>
            <username>aws</username>
            <password>${env.CODEARTIFACT_TOKEN}</password>
        </server>
    <servers>
    <mirrors>
        <mirror>
            <id>codeartifact</id>
            <name>codeartifact</name>
            <url>$MAVEN_REPO_URL</url>
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
                    <url>$MAVEN_REPO_URL</url>
                </repository>
            </repositories>
        </profile>
    </profiles>
<settings>
```

## Clean up resources<a name="tutorial-build-ba-clean"></a>

If you no longer need the resources you created for this tutorial, delete them so that you won't continue to be charged for them\. Complete the following steps:
+ Delete the CodeBuild project\. For more information, see [Delete a build project in CodeBuild](https://docs.aws.amazon.com/codebuild/latest/userguide/delete-project.html) in the *AWS CodeBuild User Guide*\.
+ Delete the CodeCommit repository\. For more information, see [Delete an CodeCommit repository](https://docs.aws.amazon.com/codecommit/latest/userguide/how-to-delete-repository.html) in the *AWS CodeCommit User Guide*\.