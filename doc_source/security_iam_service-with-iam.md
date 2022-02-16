# How AWS Mainframe Modernization works with IAM<a name="security_iam_service-with-iam"></a>

Before you use IAM to manage access to AWS Mainframe Modernization, you should understand what IAM features are available to use with AWS Mainframe Modernization\. The following table provides a summary\.


| Service | Actions | Resource\-level permissions | Resource\-based policies | Authorization based on tags | Temporary credentials | Service\-linked roles | 
| --- | --- | --- | --- | --- | --- | --- | 
|  AWS Mainframe Modernization  |  Yes  |  No  |  No  |  Yes  |  Yes  |  No  | 

**Topics**
+ [AWS Mainframe Modernization Identity\-based policies](#security_iam_service-with-iam-id-based-policies)
+ [Authorization based on AWS Mainframe Modernization tags](#security_iam_service-with-iam-tags)
+ [AWS Mainframe Modernization IAM roles](#security_iam_service-with-iam-roles)

## AWS Mainframe Modernization Identity\-based policies<a name="security_iam_service-with-iam-id-based-policies"></a>

With IAM identity\-based policies, you can specify allowed or denied actions and resources as well as the conditions under which actions are allowed or denied\. AWS Mainframe Modernization supports specific actions, resources, and condition keys\. To learn about all of the elements that you use in a JSON policy, see [IAM JSON Policy Elements Reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html) in the *IAM User Guide*\.

### Actions<a name="security_iam_service-with-iam-id-based-policies-actions"></a>

Administrators can use AWS JSON policies to specify who has access to what\. That is, which **principal** can perform **actions** on what **resources**, and under what **conditions**\.

The `Action` element of a JSON policy describes the actions that you can use to allow or deny access in a policy\. Policy actions usually have the same name as the associated AWS API operation\. There are some exceptions, such as *permission\-only actions* that don't have a matching API operation\. There are also some operations that require multiple actions in a policy\. These additional actions are called *dependent actions*\.

Include actions in a policy to grant permissions to perform the associated operation\.

Policy actions in AWS Mainframe Modernization use the following prefix before the action: `m2:`\. For example, to grant someone permission to list the AWS Mainframe Modernization environments with the AWS Mainframe Modernization `ListEnvironments` API operation, you include the `m2:ListEnvironments` action in their policy\. Policy statements must include either an `Action` or `NotAction` element\. AWS Mainframe Modernization defines its own set of actions that describe tasks that you can perform with this service\.

To specify multiple actions in a single statement, separate them with commas as follows:

```
"Action": [
      "m2:action1",
      "m2:action2"
```

You can specify multiple actions using wildcards \(\*\)\. For example, to specify all actions that begin with the word `List`, include the following action:

```
"Action": "m2:List*"
```



The following list contains the available actions for AWS Mainframe Modernization\.

```
               [
                   "m2:CreateApplication",
                   "m2:CreateDeployment",
                   "m2:CreateEnvironment",
                   "m2:DeleteApplication",
                   "m2:DeleteEnvironment",
                   "m2:GetApplication",
                   "m2:GetBatchJobExecution",
                   "m2:GetDeployment",
                   "m2:GetEnvironment",
                   "m2:ListApplications",
                   "m2:ListBatchJobExecutions",
                   "m2:ListEnvironments",
                   "m2:StartApplication",
                   "m2:StartBatchJob",
                   "m2:UpdateApplication",
                   "m2:UpdateEnvironment"
               ]
```

### Resources<a name="security_iam_service-with-iam-id-based-policies-resources"></a>

AWS Mainframe Modernization does not support specifying resource ARNs in a policy\.

## Authorization based on AWS Mainframe Modernization tags<a name="security_iam_service-with-iam-tags"></a>

You can attach tags to AWS Mainframe Modernization resources or pass tags in a request to AWS Mainframe Modernization\. To control access based on tags, you provide tag information in the [condition element](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_condition.html) of a policy using the `m2:ResourceTag/key-name`\. 

## AWS Mainframe Modernization IAM roles<a name="security_iam_service-with-iam-roles"></a>

An [IAM role](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html) is an entity within your AWS account that has specific permissions\.

### Using temporary credentials with AWS Mainframe Modernization<a name="security_iam_service-with-iam-roles-tempcreds"></a>

You can use temporary credentials to sign in with federation, assume an IAM role, or to assume a cross\-account role\. You obtain temporary security credentials by calling AWS STS API operations such as [AssumeRole](https://docs.aws.amazon.com/STS/latest/APIReference/API_AssumeRole.html) or [GetFederationToken](https://docs.aws.amazon.com/STS/latest/APIReference/API_GetFederationToken.html)\. 

AWS Mainframe Modernization supports using temporary credentials\. 

### Service roles<a name="security_iam_service-with-iam-roles-service"></a>

This feature allows a service to assume a [service role](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_terms-and-concepts.html#iam-term-service-role) on your behalf\. This role allows the service to access resources in other services to complete an action on your behalf\. Service roles appear in your IAM account and are owned by the account\. This means that an IAM administrator can change the permissions for this role\. However, doing so might break the functionality of the service\.

AWS Mainframe Modernization supports service roles\. 