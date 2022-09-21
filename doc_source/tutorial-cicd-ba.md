# Tutorial: Setting up a CI/CD pipeline for use with Blu Age Developer<a name="tutorial-cicd-ba"></a>

To set up a CI/CD pipeline for Blu Age, complete the following steps in [Tutorial: Setting up the build for the Planets demo app](tutorial-build-ba.md)\.
+ [Step 1: Push the source code to the repo](tutorial-build-ba.md#tutorial-build-ba-step1)
+ [Step 2: Generate the Maven config](tutorial-build-ba.md#tutorial-build-ba-step2)
+ [Step 3: Deploy Blu Age dependencies into the CodeArtifact repository](tutorial-build-ba.md#tutorial-build-ba-step3)

Next, complete the following steps to deploy a code pipeline using AWS CodePipeline\.

## Step 1: Deploy the CodePipeline stack and check the pipeline<a name="tutorial-cicd-ba-step1"></a>

1. Update the parameter file `build-pipeline-params.json` with the corresponding values for `CodeRepositoryName`, `ArtifactRepositoryName`, and `ArtifactRepositoryDomain`\.

1. Deploy the stack `build-pipeline.yaml`\.

   ```
   aws cloudformation deploy --stack-name {your-stack-name} --template-file build-pipeline.yaml \
        --capabilities CAPABILITY_IAM --parameter-overrides file://build-pipeline-params.json
   ```

   Now, an CodePipeline code pipeline should be created and connected to the CodeCommit repository\. Check the build stage for errors\.

Finally, complete the steps in [Step 4: Deploy the CodeBuild project](tutorial-build-ba.md#tutorial-build-ba-step4)

## Clean up resources<a name="tutorial-cicd-ba-clean"></a>

If you no longer need the resources you created for this tutorial, delete them so that you won't continue to be charged for them\. Complete the following steps:
+ Delete the CodePipeline pipeline\. For more information, see [Delete a pipeline in CodePipeline](https://docs.aws.amazon.com/codepipeline/latest/userguide/pipelines-delete.html) in the *AWS CodePipeline User Guide*\.
+ Delete the CodeBuild project\. For more information, see [Delete a build project in CodeBuild](https://docs.aws.amazon.com/codebuild/latest/userguide/delete-project.html) in the *AWS CodeBuild User Guide*\.
+ Delete the CodeCommit repository\. For more information, see [Delete an CodeCommit repository](https://docs.aws.amazon.com/codecommit/latest/userguide/how-to-delete-repository.html) in the *AWS CodeCommit User Guide*\.
+ Delete the CodeArtifact repository\. For more information, see [Delete a repository](https://docs.aws.amazon.com/codeartifact/latest/ug/delete-repo.html) in the *CodeArtifact User Guide*\.