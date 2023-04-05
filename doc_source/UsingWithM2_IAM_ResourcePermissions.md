# AWS Mainframe Modernization API permissions: Actions, resources, and conditions reference<a name="UsingWithM2_IAM_ResourcePermissions"></a>

When you are writing permissions policies that you can attach to an IAM identity \(identity\-based policies\), you can use the following table as a reference\. The table includes the following:
+ Each AWS Mainframe Modernization API operation
+ The corresponding actions for which you can grant permissions to perform the action
+ The AWS resource for which you can grant the permissions

 You specify the actions in the policy's `Action` field and the resource value in the policy's `Resource` field\. 

You can use AWS global condition keys in your AWS Mainframe Modernization policies to express conditions\. For a complete list of AWS keys, see [Available Global Condition Keys](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#AvailableKeys) in the *IAM User Guide*\. 

**Note**  
To specify an action, use the `m2:` prefix followed by the API operation name \(for example, `m2:CreateApplication`\)\.


**AWS Mainframe Modernization API and required permissions for actions**  

| AWS Mainframe Modernization API Operations | Required Permissions \(API Actions\) | Resources | 
| --- | --- | --- | 
|  [CancelBatchJobExecution](m2/latest/APIReference/API_CancelBatchJobExecution.html)  |  |  Application  | 
| [CreateApplication](m2/latest/APIReference/API_CreateApplication.html)  |  `s3:GetObject` `s3:ListBucket `  | Application | 
| [CreateDataSetImportTask](m2/latest/APIReference/API_CreateDataSetImportTask.html)  |  `m2:CreateDataSetImportTask` `s3:GetObject` | Application | 
| [CreateDeployment](m2/latest/APIReference/API_CreateDeployment.html)  |  `elasticloadbalancing:AddTags` `elasticloadbalancing:CreateListener` `elasticloadbalancing:CreateTargetGroup` `elasticloadbalancing:RegisterTargets`  | Application | 
|   [CreateEnvironment](m2/latest/APIReference/API_CreateEnvironment.html)   |  `ec2:CreateNetworkInterface` `ec2:CreateNetworkInterfacePermission` `ec2:DescribeNetworkInterfaces` `ec2:DescribeSecurityGroups` `ec2:DescribeSubnets` `ec2:DescribeVpcAttribute` `ec2:DescribeVpcs` `ec2:ModifyNetworkInterfaceAttribute` `elasticfilesystem:DescribeMountTargets` `elasticloadbalancing:AddTags` `elasticloadbalancing:CreateLoadBalancer` `fsx:DescribeFileSystems` `iam:CreateServiceLinkedRole`  |  Environment  | 
|   [DeleteApplication](m2/latest/APIReference/API_DeleteApplication.html)   |  `elasticloadbalancing:DeleteListener` `elasticloadbalancing:DeleteTargetGroup` `logs:DeleteLogDelivery`  |  Application  | 
|   [DeleteApplicationFromEnvironment](m2/latest/APIReference/API_DeleteApplicationFromEnvironment.html)   |  `elasticloadbalancing:DeleteListener` `elasticloadbalancing:DeleteTargetGroup`  |  Application Environment  | 
|   [DeleteEnvironment](m2/latest/APIReference/API_DeleteEnvironment.html)   |  `elasticloadbalancing:DeleteLoadBalancer`  |  Environment  | 
|   [GetApplication](m2/latest/APIReference/API_GetApplication.html)   |   |  Application  | 
| [GetApplicationVersion](m2/latest/APIReference/API_GetApplicationVersion.html)  |  | Application | 
|   [GetBatchJobExecution](m2/latest/APIReference/API_GetBatchJobExecution.html)   |   |  Application  | 
|   [GetDataSetDetails](m2/latest/APIReference/API_GetDataSetDetails.html)   |   |  Application  | 
|   [GetDataSetImportTask](m2/latest/APIReference/API_GetDataSetImportTask.html)   |   |  Application  | 
|   [GetDeployment](m2/latest/APIReference/API_GetDeployment.html)   |   |  Application  | 
|   [GetEnvironment](m2/latest/APIReference/API_GetEnvironment.html)   |   |  Environment  | 
| [ListApplications](m2/latest/APIReference/API_ListApplications.html)  |  | \* | 
|   [ListApplicationVersions](m2/latest/APIReference/API_ListApplicationVersions.html)   |   |  \*  | 
|   [ListBatchJobDefinitions](m2/latest/APIReference/API_ListBatchJobDefinitions.html)   |   |  \*  | 
|   [ListBatchJobExecutions](m2/latest/APIReference/API_ListBatchJobExecutions.html)   |  ``  |  \*  | 
|   [ListDataSetImportHistory](m2/latest/APIReference/API_ListDataSetImportHistory.html)   |   |  \*  | 
|   [ListDataSets](m2/latest/APIReference/API_ListDataSets.html)   |   |  \*  | 
| [ListDeployments](m2/latest/APIReference/API_ListDeployments.html)  |  | \* | 
|   [ListEngineVersions](https://docs.aws.amazon.com/m2/latest/APIReference/API_ListEngineVersions.html)   |   |  \*  | 
| [ListEnvironments](m2/latest/APIReference/API_ListEnvironments.html)  |  | \* | 
|   [ListTagsForResource](m2/latest/APIReference/API_ListTagsForResource.html)   |    |  \*  | 
|   [StartApplication](m2/latest/APIReference/API_StartApplication.html)   |    |  Application  | 
|   [StartBatchJob](m2/latest/APIReference/API_StartBatchJob.html)   |   |  Application  | 
|   [StopApplication](m2/latest/APIReference/API_StopApplication.html)   |   |  Application  | 
|   [TagResource](m2/latest/APIReference/API_TagResource.html)   |   |  \*  | 
|   [UntagResource](m2/latest/APIReference/API_UntagResource.html)   |   |  \*  | 
|   [UpdateApplication](m2/latest/APIReference/API_UpdateApplication.html)   |  `s3:GetObject` `s3:ListBucket`  |  Application  | 
|   [UpdateEnvironment](m2/latest/APIReference/API_UpdateEnvironment.html)   |    |  Environment  | 