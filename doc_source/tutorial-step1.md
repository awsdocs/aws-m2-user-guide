# Use Case 1 \- Using the COBOL Project Template containing source components<a name="tutorial-step1"></a>

This use case requires you to copy the source components into the Template directory structure as part of the demo pre setup steps\. In the [https://d1vi4vxke6c2hu.cloudfront.net/demo/bankdemo.zip](https://d1vi4vxke6c2hu.cloudfront.net/demo/bankdemo.zip) this has been changed from the original `AWSTemplates.zip` delivery to avoid having two copies of the source\.

1. Start Enterprise Developer and specify the chosen workspace\.  
![\[Select a directory as workspace\]](http://docs.aws.amazon.com/m2/latest/userguide/images/ed-uc1-step1.png)

1. Within the **Application Explorer** view, from the **Enterprise Development Project** tree view item, choose **New Project from Template** from the context menu\.  
![\[New project from template\]](http://docs.aws.amazon.com/m2/latest/userguide/images/ed-uc1-step2.png)

1. Enter the template parameters as shown\.
**Note**  
The Template Path will refer to where the ZIP was extracted\.  
![\[Enter template parameters\]](http://docs.aws.amazon.com/m2/latest/userguide/images/ed-uc1-step3.png)

1. Choosing OK will create a local development Eclipse Project based on the provided template, with a complete source and execution environment structure\.  
![\[Local development Eclipse project\]](http://docs.aws.amazon.com/m2/latest/userguide/images/ed-uc1-step4.png)

   The `System` structure contains a complete resource definition file with the required entries for BANKDEMO, the required catalog with entries added and the corresponding ASCII data files\.

   Because the source template structure contains all the source items, these files are copied to the local project and therefore are automatically built in ED\.