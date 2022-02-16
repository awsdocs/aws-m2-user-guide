# Use Case 2 \- Using the COBOL Project Template without source components<a name="tutorial-step2"></a>

Steps 1 to 3 are identical to [Use Case 1 \- Using the COBOL Project Template containing source components](tutorial-step1.md)\. 

The `System` structure in this use case also contains a complete resource definition file with the required entries for BankDemo, the required catalog with entries added, and the corresponding ASCII data files\.

However, the template source structure does not contain any components\. You must import these into the project from whatever source repository you are using\.

1. Choose the project name\. From the related context menu, choose **Import**\.  
![\[Choose import\]](http://docs.aws.amazon.com/m2/latest/userguide/images/ed-uc2-step4.png)

1. From the resulting dialog, under the **General** section, choose **File System** and then choose Next\.  
![\[Choose file system\]](http://docs.aws.amazon.com/m2/latest/userguide/images/ed-uc2-step5.png)

1. Populate the **From directory** field by browsing the file system to point to the repository folder\. Choose all the folders you wish to import, such as `sources`\. The `Into folder` field will be pre\-populated\. Choose **Finish**\.   
![\[File system\]](http://docs.aws.amazon.com/m2/latest/userguide/images/ed-uc2-step6.png)

   After the source template structure contains all the source items, they are built automatically in ED\.