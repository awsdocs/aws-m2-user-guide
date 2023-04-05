# Access AWS Mainframe Modernization using an interface endpoint \(AWS PrivateLink\)<a name="vpc-interface-endpoints"></a>

You can use AWS PrivateLink to create a private connection between your VPC and AWS Mainframe Modernization\. You can access Mainframe Modernization as if it were in your VPC, without the use of an internet gateway, NAT device, VPN connection, or AWS Direct Connect connection\. Instances in your VPC don't need public IP addresses to access Mainframe Modernization\.

You establish this private connection by creating an *interface endpoint*, powered by AWS PrivateLink\. We create an endpoint network interface in each subnet that you enable for the interface endpoint\. These are requester\-managed network interfaces that serve as the entry point for traffic destined for Mainframe Modernization\.

For more information, see [Access AWS services through AWS PrivateLink](https://docs.aws.amazon.com/vpc/latest/privatelink/privatelink-access-aws-services.html) in the *AWS PrivateLink Guide*\.

## Considerations for Mainframe Modernization<a name="vpc-endpoint-considerations"></a>

Before you set up an interface endpoint for Mainframe Modernization, review [Considerations](https://docs.aws.amazon.com/vpc/latest/privatelink/create-interface-endpoint.html#considerations-interface-endpoints) in the *AWS PrivateLink Guide*\.

Mainframe Modernization supports making calls to all of its API actions through the interface endpoint\.

## Create an interface endpoint for Mainframe Modernization<a name="vpc-endpoint-create"></a>

You can create an interface endpoint for Mainframe Modernization using either the Amazon VPC console or the AWS Command Line Interface \(AWS CLI\)\. For more information, see [Create an interface endpoint](https://docs.aws.amazon.com/vpc/latest/privatelink/create-interface-endpoint.html#create-interface-endpoint-aws) in the *AWS PrivateLink Guide*\.

Create an interface endpoint for Mainframe Modernization using the following service name:

```
com.amazonaws.region.m2
```

If you enable private DNS for the interface endpoint, you can make API requests to Mainframe Modernization using its default Regional DNS name\. For example, `m2.us-east-1.amazonaws.com`\.

## Create an endpoint policy for your interface endpoint<a name="vpc-endpoint-policy"></a>

An endpoint policy is an IAM resource that you can attach to an interface endpoint\. The default endpoint policy allows full access to Mainframe Modernization through the interface endpoint\. To control the access allowed to Mainframe Modernization from your VPC, attach a custom endpoint policy to the interface endpoint\.

An endpoint policy specifies the following information:
+ The principals that can perform actions \(AWS accounts, users, and IAM roles\)\.
+ The actions that can be performed\.
+ The resources on which the actions can be performed\.

For more information, see [Control access to services using endpoint policies](https://docs.aws.amazon.com/vpc/latest/privatelink/vpc-endpoints-access.html) in the *AWS PrivateLink Guide*\.

**Example: VPC endpoint policy for Mainframe Modernization actions**  
The following is an example of a custom endpoint policy\. When you attach this policy to your interface endpoint, it grants access to the listed Mainframe Modernization actions for all principals on all resources\.

```
//Example of an endpoint policy where access is granted to the 
//listed AWS Mainframe Modernization actions for all principals on all resources
{"Statement": [
      {"Principal": "*",
         "Effect": "Allow",
         "Action": [
            "m2:ListApplications",
            "m2:ListEnvironments",
            "m2:ListDeployments"
         ],
         "Resource":"*"
      }
   ]
}

//Example of an endpoint policy where access is denied to all the 
//AWS Mainframe Modernization CREATE actions for all principals on all resources
{"Statement": [
      {"Principal": "*",
         "Effect": "Deny",
         "Action": [
            "m2:Create*"
         ],
         "Resource":"*"
      }
   ]
}
```