# Tutorial: Setting up a CI/CD pipeline for use with Micro Focus Enterprise Developer<a name="tutorial-cicd-mf"></a>

This tutorial shows you how to import, edit, compile, and run the BankDemo sample application in Micro Focus Enterprise Developer, and then to commit your changes to trigger a CI/CD pipeline\.

**Contents**
+ [Prerequisites](#tutorial-cicd-mf-prereq)
+ [Create CI/CD pipeline basic infrastructure](#tutorial-cicd-mf-basic)
+ [Create AWS CodeCommit repository and CI/CD pipeline](#tutorial-cicd-mf-repo)
  + [Sample YAML Trigger File config\_git\.yml](#tutorial-cicd-mf-repo-sample)
+ [Enterprise Developer AppStream 2\.0 Creation](#tutorial-cicd-mf-aas)
+ [Enterprise Developer Setup and Test](#tutorial-cicd-mf-setup-test)
  + [Clone the BankDemo CodeCommit repository in Enterprise Developer](#tutorial-cicd-mf-setup-test-clone)
  + [Create BankDemo mainframe COBOL project and build application](#tutorial-cicd-mf-setup-test-create-cobol)
  + [Create local BankDemo CICS and batch environment for testing](#tutorial-cicd-mf-setup-test-create-cics)
  + [Start the BANKDEMO server from Enterprise Developer](#tutorial-cicd-mf-setup-test-start-bankdemo)
  + [Start the Rumba 3270 terminal](#tutorial-cicd-mf-setup-test-start-3270)
  + [Run a BankDemo transaction](#tutorial-cicd-mf-setup-test-run-bankdemo)
  + [Stop the BANKDEMO server from Enterprise Developer](#tutorial-cicd-mf-setup-test-stop-bankdemo)
+ [Exercise 1: Enhance Loan Calculation in BANKDEMO Application](#tutorial-cicd-mf-exer-1)
  + [Add loan analysis rule to Enterprise Developer Code Analysis](#tutorial-cicd-mf-exer-1-add-rule)
  + [Step 1: Perform code analysis for loan calculation](#tutorial-cicd-mf-exer-1-code-analysis)
  + [Step 2: Modify CICS BMS map and COBOL program and test](#tutorial-cicd-mf-exer-1-cics-bms)
  + [Step 3: Add total amount calculation in COBOL program](#tutorial-cicd-mf-exer-1-add-calculation)
  + [Step 4: Commit changes and run CI/CD pipeline](#tutorial-cicd-mf-exer-1-commit-changes)
+ [Exercise 2: Extract loan calculation in BankDemo application](#tutorial-cicd-mf-exer-2)
  + [Step 1: Refactor loan calculation routine into a COBOL section](#tutorial-cicd-mf-exer-2-extract-loan)
  + [Step 2: Extract loan calculation routine to a standalone COBOL program](#tutorial-cicd-mf-exer-2-standalone)
  + [Step 3: Commit changes and run the CI/CD pipeline](#tutorial-cicd-mf-exer-2-commit-run)
+ [Clean up resources](#tutorial-cicd-mf-clean)

## Prerequisites<a name="tutorial-cicd-mf-prereq"></a>

Download the the following files\.
+ `basic-infra.yaml`\.
  + [Download from Europe \(Frankfurt\) Region](https://d3lkpej5ajcpac.cloudfront.net/cicd/mf/basic-infra.yaml)\.
  + [Download from US East \(N\. Virginia\) Region](https://drm0z31ua8gi7.cloudfront.net/cicd/mf/basic-infra.yaml)\.
+ `pipeline.yaml`\.
  + [Download from Europe \(Frankfurt\) Region](https://d3lkpej5ajcpac.cloudfront.net/cicd/mf/pipeline.yaml)\.
  + [Download from US East \(N\. Virginia\) Region](https://drm0z31ua8gi7.cloudfront.net/cicd/mf/pipeline.yaml)\.
+ `m2-code-sync-function.zip`\.
  + [Download from Europe \(Frankfurt\) Region](https://d3lkpej5ajcpac.cloudfront.net/cicd/mf/m2-code-sync-function.zip)\.
  + [Download from US East \(N\. Virginia\) Region](https://drm0z31ua8gi7.cloudfront.net/cicd/mf/m2-code-sync-function.zip)\.
+ `config_git.yml`\.
  + [Download from Europe \(Frankfurt\) Region](https://d3lkpej5ajcpac.cloudfront.net/cicd/mf/config_git.yml)\.
  + [Download from US East \(N\. Virginia\) Region](https://drm0z31ua8gi7.cloudfront.net/cicd/mf/config_git.yml)\.
+ `BANKDEMO-source.zip`\.
  + [Download from Europe \(Frankfurt\) Region](https://d3lkpej5ajcpac.cloudfront.net/cicd/mf/BANKDEMO-source.zip)\.
  + [Download from US East \(N\. Virginia\) Region](https://drm0z31ua8gi7.cloudfront.net/cicd/mf/BANKDEMO-source.zip)\.
+ `BANKDEMO-exercise.zip`\.
  + [Download from Europe \(Frankfurt\) Region](https://d3lkpej5ajcpac.cloudfront.net/cicd/mf/BANKDEMO-exercise.zip)\.
  + [Download from US East \(N\. Virginia\) Region](https://drm0z31ua8gi7.cloudfront.net/cicd/mf/BANKDEMO-exercise.zip)\.

The purpose of each file is as follows:

`basic-infra.yaml`  
This AWS CloudFormation template creates the basic infrastructure needed for the CI/CD pipeline: VPC, Amazon S3 buckets, and so on\.

`pipeline.yaml`\.  
This AWS CloudFormation template is used by an Lambda function to launch the pipeline stack\. Make sure this template is located in a publicly accessible Amazon S3 bucket\. Add the link to this bucket as the default value for the `PipelineTemplateURL`parameter in the `basic-infra.yaml` template\.

`m2-code-sync-function.zip`\.  
This Lambda function creates the CodeCommit repository, the directory structure based on the `config_git.yaml`, and launches the pipeline stack using `pipeline.yaml`\. Make sure this zip file is available in a publicly accessible Amazon S3 bucket in all the AWS Regions where AWS Mainframe Modernization is supported\. We recommend that you store the file in a bucket in one AWS Region and replicate it to buckets across all AWS Regions\. Use a naming convention for the bucket with a suffix that identifies the specific AWS Region \(for example, `m2-cicd-deployment-source-eu-west-1` \) and add the prefix `m2-cicd-deployment-source` as default value for parameter `DeploymentSourceBucket` and form the full bucket by using the AWS CloudFormation substitution function `!Sub {DeploymentSourceBucket}-${AWS::Region}` while referring to that bucket in the `basic-infra.yaml` template for resource `SourceSyncLambdaFunction`\.

`config_git.yml`\.  
 CodeCommit directory structure definition\. For more information, see [Sample YAML Trigger File config\_git\.yml](#tutorial-cicd-mf-repo-sample)\.

`BANKDEMO-source.zip`\.  
BankDemo source code and configuration file created from the CodeCommit repository\.

`BANKDEMO-exercise.zip`\.  
 BankDemo source for tutorial exercises created from the CodeCommit repository\.

## Create CI/CD pipeline basic infrastructure<a name="tutorial-cicd-mf-basic"></a>

Open the following link to quick launch the AWS Mainframe Modernization CI/CD Pipeline Foundation stack through AWS CloudFormation in the AWS Region of your choice \(you should have already logged in to your AWS Management Console while opening the link\)\. This stack will create an Amazon S3 bucket where you need to upload your application code \(instructions are stated in next section\) and supporting Lambda function to create the necessary resources like CodeCommit repository, CodePipeline pipeline, etc\.

**Note**  
To launch this stack you need permissions to administer IAM, Amazon S3, Lambda, and AWS CloudFormation and permissions to use AWS KMS\.

1. Click one of the following links to launch AWS CloudFormation and open the **Quick create stack** page:
   + Launch stack in US East \(N\. Virginia\)
   + Launch stack in US West \(Oregon\)
   + Launch stack in Europe \(Frankfurt\)
   + Launch stack in South America \(São Paulo\)
   + Launch stack in Asia Pacific \(Sydney\)

   All the parameters are pre\-populated appropriately so you don’t need to modify them\.
**Note**  
Don’t change the parameters unless you have modified the AWS CloudFormation template yourself\.   
![\[The quick create stack page with AWS Mainframe Modernization values populated.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/cfn-quick-create.png)

1. In **Capabilities**, choose **I acknowledge that AWS CloudFormation might create IAM resources** to allow permission for AWS CloudFormation to create IAM Role on your behalf\. Choose **Create stack**\.
**Note**  
It can take 3 to 5 minutes for this stack to be provisioned\.

1. After the stack has been created successfully, navigate to the **Outputs** section of the newly provisioned stack\. There you'll find the Amazon S3 bucket where you need to upload your mainframe code and dependent files\.  
![\[The AWS CloudFormation Outputs tab, with the AWS Mainframe Modernization Amazon S3 buckets displayed.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/cfn-outputs.png)

## Create AWS CodeCommit repository and CI/CD pipeline<a name="tutorial-cicd-mf-repo"></a>

 In this step, you create a CodeCommit repository and provision a CI/CD pipeline stack by calling a Lambda function that calls AWS CloudFormation to create the pipeline stack\. 

1. Download the [BankDemo sample application](https://d1vi4vxke6c2hu.cloudfront.net/demo/bankdemo.zip) to your local machine\.

1. Upload `bankdemo.zip` from your local machine to the Amazon S3 bucket created in [Create CI/CD pipeline basic infrastructure](#tutorial-cicd-mf-basic)\.

1. Download `config_git.yml`\.

1. Modify the `config_git.yml` if needed, as follows:
   + Add your own target repository name, target branch and commit message\.

     ```
     repository-config:
       target-repository: bankdemo-repo
       target-branch: main
       commit-message: Initial commit for bankdemo-repo main branch
     ```
   + Add the email address you want to receive notifications\.

     ```
     pipeline-config:
       # Send pipeline failure notifications to these email addresses
       alert-notifications:
         - myname@mycompany.com
       # Send notifications for manual approval before production deployment to these email addresses
       approval-notifications:
         - myname@mycompany.com
     ```

1. Upload the `config_git.yml` file containing the definition of the CodeCommit repository folder structure to the Amazon S3 bucket created in [Create CI/CD pipeline basic infrastructure](#tutorial-cicd-mf-basic)\. This will invoke the Lambda function that will automatically provision the repository and pipeline\.

   This will create a CodeCommit repository with the name provided in the `target-repository` defined in the `config_git.yml` file; for example, `bankdemo-repo`\.

   The Lambda function will also create the CI/CD pipeline stack through AWS CloudFormation\. The AWS CloudFormation stack will have the same prefix as the `target-repository` name provided followed by a random string \(for example `bankdemo-repo-01234567`\. You can find the CodeCommit repository URL and the URL to access the created pipeline in the AWS Management Console\.  
![\[AWS CloudFormation Outputs tab showing the endpoints for the CodeCommit repository and CodePipeline pipeline created on your behalf.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/cfn-outputs-endpoints.png)

1. If the CodeCommit repository creation is complete, the CI/CD pipeline will be triggered immediately to perform a full CI/CD\.

1. Once the file has been pushed it will automatically trigger the pipeline which will build, deploy in staging, run some tests and wait for manual approval before getting it deployed in the production environment\.

### Sample YAML Trigger File config\_git\.yml<a name="tutorial-cicd-mf-repo-sample"></a>

```
repository-config:
  target-repository: bankdemo-repo
  target-branch: main
  commit-message: Initial commit for bankdemo-repo main branch
  directory-structure:
    - '/':
        files:
        - build.xml
        - '*.yaml'
        - '*.yml'
        - '*.xml'
        - 'LICENSE.txt'
        readme: |
          # Root Folder
          - 'build.xml' : Build configuration for the application
    - tests:
        files:
        - '*.py'
        readme: |
          # Test Folder
          - '*.py' : Test scripts
    - config:
        files:
        - 'BANKDEMO.csd'
        - 'BANKDEMO.json'
        - 'BANKDEMO_ED.json'
        - 'dfhdrdat'
        - 'ESPGSQLXA.dll'
        - 'ESPGSQLXA64.so'
        - 'ESPGSQLXA64_S.so'
        - 'EXTFH.cfg'
        - 'm2-2021-04-28.normal.json'
        - 'MFDBFH.cfg'
        - 'application-definition-template-config.json'
        readme: |
          # Config Folder
          This folder contains the application configuration files.
          - 'BANKDEMO.csd'     : CICS Resource definitions export file 
          - 'BANKDEMO.json'    : Enterprise Server configuration
          - 'BANKDEMO_ED.json' : Enterprise Server configuration for ED 
          - 'dfhdrdat'         : CICS resource definition file
          - 'ESPGSQLXA.dll'    : XA switch module Windows
          - 'ESPGSQLXA64.so'   : XA switch module Linux
          - 'ESPGSQLXA64_S.so' : XA switch module Linux
          - 'EXTFH.cfg'        : Micro Focus File Handler configuration
          - 'm2-2021-04-28.normal.json' : M2 request document
          - 'MFDBFH.cfg'       : Micro Focus Database File Handler
          - 'application-definition-template-config.json' : Application definition for M2
    - source:
        subdirs:
        - .settings:
            files:
              - '.bms.mfdirset'
              - '.cbl.mfdirset'              
        - copybook:
            files:
              - '*.cpy'
              - '*.inc'
            readme: |
              # Copy folder
              This folder contains the source for COBOL copy books, PLI includes, ...
              - .cpy COBOL copybooks
              - .inc PLI includes
#        - ctlcards:
#            files:
#              - '*.ctl'
#              - 'KBNKSRT1.txt'
#            readme: |
#              # Control Card folder
#              This folder contains the source for Batch Control Cards
#              - .ctl Control Cards
        - ims:
            files:
              - '*.dbd'
              - '*.psb'
            readme: |
              # ims folder
              This folder contains the IMS DB source files with the extensions
              - .dbd for IMS DBD source
              - .psb for IMS PSB source
        - jcl:
            files:
              - '*.jcl'
              - '*.ctl'
              - 'KBNKSRT1.txt'
              - '*.prc'
            readme: |
              # jcl folder
              This folder contains the JCL source files with the extensions
              - .jcl
#        - proclib:
#            files:
#              - '*.prc'
#            readme: |
#              # proclib folder
#              This folder contains the JCL procedures referenced via PROCLIB statements in the JCL with extensions
#              - .prc
        - rdbms:
            files:
              - '*.sql'
            readme: |
              # rdbms folder
              This folder contains any DB2 related source files with extensions
              - .sql for any kind of SQL source
        - screens:
            files:
            - '*.bms'
            - '*.mfs'
            readme: |
              # screens folder
              This folder contains the screens source files with the extensions
              - .bms for CICS BMS screens
              - .mfs for IMS MFS screens
            subdirs:
            - .settings:
                files:
                    - '*.bms.mfdirset'
        - cobol:
            files:
              - '*.cbl'
              - '*.pli'
            readme: |
              # source folder
              This folder contains the program source files with the extensions
              - .cbl for COBOL source
              - .pli for PLI source
            subdirs:
            - .settings:
                files:
                   - '*.cbl.mfdirset'
    - tests:
        files:
        - 'test_script.py'
        readme: |
          # tests Folder
          This folder contains the application test scripts    
pipeline-config:
  alert-notifications:
    - myname@mycompany.com
  approval-notifications:
    - myname@mycompany.com
```

## Enterprise Developer AppStream 2\.0 Creation<a name="tutorial-cicd-mf-aas"></a>

To set up Micro Focus Enterprise Developer on AppStream 2\.0, see [Tutorial: Set up Micro Focus Enterprise Developer on AppStream 2\.0](set-up-ed.md)\.

To connect the CodeCommit repository to Enterprise Developer, use the name specified in `target-repository` in [Sample YAML Trigger File config\_git\.yml](#tutorial-cicd-mf-repo-sample)\.

## Enterprise Developer Setup and Test<a name="tutorial-cicd-mf-setup-test"></a>

**Topics**
+ [Clone the BankDemo CodeCommit repository in Enterprise Developer](#tutorial-cicd-mf-setup-test-clone)
+ [Create BankDemo mainframe COBOL project and build application](#tutorial-cicd-mf-setup-test-create-cobol)
+ [Create local BankDemo CICS and batch environment for testing](#tutorial-cicd-mf-setup-test-create-cics)
+ [Start the BANKDEMO server from Enterprise Developer](#tutorial-cicd-mf-setup-test-start-bankdemo)
+ [Start the Rumba 3270 terminal](#tutorial-cicd-mf-setup-test-start-3270)
+ [Run a BankDemo transaction](#tutorial-cicd-mf-setup-test-run-bankdemo)
+ [Stop the BANKDEMO server from Enterprise Developer](#tutorial-cicd-mf-setup-test-stop-bankdemo)

Connect to the Enterprise Developer AppStream 2\.0 instance you created in [Enterprise Developer AppStream 2\.0 Creation](#tutorial-cicd-mf-aas)\.

1. Start Enterprise Developer from Windows Start\. Choose Micro Focus Enterprise Developer, then choose Enterprise Developer for Eclipse\. If you are starting for the first time, it might take some time\.

1. In the Eclipse Launcher, in **Workspace**: enter `C:\Users\<username>\workspace` then choose **Launch**\.
**Note**  
Make sure you choose the same location after reconnecting to the AppStream 2\.0 instance\. Workspace selection is not persistent\.

1. In **Welcome**, choose **Open COBOL Perspective**\. This will only be shown the first time for a new workspace\.

### Clone the BankDemo CodeCommit repository in Enterprise Developer<a name="tutorial-cicd-mf-setup-test-clone"></a>

1. Choose **Window / Perspective / Open Perspective / Other \.\.\. / Git**\.

1. Choose **Clone a Git repository**\. 

1. In **Clone Git Repository**, enter the following information:
   + In **Location URI**, enter the HTTPS URL of the CodeCommit repository\.
**Note**  
Copy the Clone URL HTTPS for the CodeCommit repository in the AWS Management Console and paste it here\. The URI will be split into the **Host** and **Repository** paths\.\. 
   + The IAM user CodeCommit repository credentials in **Authentication User** and **Password** and choose **Store** in **Secure Store**\.

1. In **Branch Selection**, choose **Main** branch, then choose **Next**\.

1. In **Local Destination**, in **Directory**, enter `C:\Users\<username>\workspace` and choose **Finish**\.

   The clone process is completed when `BANKDEMO [main]` is shown in the **Git Repositories** view\.

### Create BankDemo mainframe COBOL project and build application<a name="tutorial-cicd-mf-setup-test-create-cobol"></a>

1. Change to **COBOL Perspective**\.

1. In **Project**, disable **Build Automatically**\.

1. In **File**, choose **New**, then **Mainframe COBOL Project**\.

1. In **New Mainframe COBOL Project**, enter the following information:
   + In **Project name**, enter `BankDemo`\.
   + Choose **Micro Focus template \[64 bit\]**\.
   + Choose **Finish**\.

1. In **COBOL Explorer**, expand the new BankDemo project\.
**Note**  
`[BANKDEMO main]` in square brackets indicates that the project is connected with the local BankDemo CodeCommit repository\.

1. If the tree view does not show entries for COBOL Programs, Copybooks, BMS Source, and JCL Files, choose **Refresh** from the BankDemo project context menu\.

1. From the BankDemo context menu, choose **Properties / Micro Focus / Project Settings / COBOL**:
   + Choose **Character Set \- ASCII**\.
   + Choose **Apply**, then **Close**\. 

1. If the build of the BMS and COBOL source does not immediately start, check in the **Project** menu, that the option **Build Automatically** is enabled\.

   The Build output will be displayed in the **Console** view and should complete after a few minutes with messages `BUILD SUCCESSFUL` and `Build finished with no errors`\.

   The BankDemo application should now be compiled and ready for local execution\.

### Create local BankDemo CICS and batch environment for testing<a name="tutorial-cicd-mf-setup-test-create-cics"></a>

1. In **COBOL Explorer**, expand `BANKDEMO / config`\.

1. In the editor, open `BANKDEMO_ED.json`\.

1. Find string `ED_Home=` and change path to point to the Enterprise Developer project, as follows: `D:\\<username>\\workspace\\BANKDEMO`\. Note the use of double slashes \(`\\`\)in the path definition\.

1. Save and close the file\.

1. Choose **Server Explorer**\.

1. From the **Default** context menu, choose **Open Administration Page**\. The Micro Focus Enterprise Server **Administration** page is opened in the default browser\.

1. For AppStream 2\.0 sessions only, make the following changes so you can preserve your local Enterprise Server region for local testing:
   + In **Directory Server / Default**, choose **PROPERTIES / Configuration**\.
   + Replace **Repository Location** with `D:\<username>\My Files\Home Folder\MFDS`\.
**Note**  
You must complete steps 5 \- 8 after every new connection to an AppStream 2\.0 instance\.

1. In **Directory Server / Default**, choose **Import**, then complete the following steps:
   + In **Step 1: Import Type**, choose **JSON** and choose **Next**\.
   + In **Step 2: Upload**, click to upload file in blue square\.
   + In **Choose File to Upload**, enter:
     + **File name:** `D:\<username>\workspace\BANKDEMO\config\BANKDEMO_ED.json`\.
     + Choose **Open**\.
   + Choose **Next**\.
   + In **Step 3: Regions** clear **Clear Ports from Endpoints**\.
   + Choose **Next**\.
   + In **Step 4: Import**, choose **Import**\.
   + Choose **Finish**\.

   The list will now show a new server name `BANKDEMO`\.

### Start the BANKDEMO server from Enterprise Developer<a name="tutorial-cicd-mf-setup-test-start-bankdemo"></a>

1. Choose **Enterprise Developer**\.

1. In **Server Explorer**, choose **Default**, then choose **Refresh** from the context menu\.

   The server list should now also show BANKDEMO\.

1. Choose **BANKDEMO**\.

1. From the context menu, choose **Associate with project**, then choose **BANKDEMO**\.

1. From the context menu, choose **Start**\. 

   The Console view should display the log for the server startup\.

   If the message `BANKDEMO CASSI5030I PLTPI Phase 2 List(PI) Processing Completed` is displayed, the Server is ready for testing the CICS BANKDEMO application\.

### Start the Rumba 3270 terminal<a name="tutorial-cicd-mf-setup-test-start-3270"></a>

1. From Windows Start, launch Micro Focus Rumba\+ Desktop / Rumba\+ Desktop\.

1. In **Welcome**, choose **CREATE NEW SESSION / Mainframe Display**\.

1. In **Mainframe Display**, choose **Connection / Configure**\.

1. In **Session Configuration**, choose **Connection / TN3270**\.

1. In **Host Name / Address**, choose **Insert** and enter IP address `127.0.0.1`\.

1. In **Telnet Port**, enter port `6000`\.

1. Choose **Apply**\.

1. Choose **Connect**\.

   The CICS welcome screen displays screen with row 1 message: `This is the Micro Focus MFE CICS region BANKDEMO`\.

1. Press CTRL\+Shift\+Z to clear screen\.

### Run a BankDemo transaction<a name="tutorial-cicd-mf-setup-test-run-bankdemo"></a>

1. In an empty screen, enter `BANK`\.

1. In screen **BANK10**, in the input field for **User id\.\.\.\.\.:**, enter `guest` and press Enter\.

1. In screen **BANK20**, in the input field before **Calculate the cost of a loan**, enter `/` \(forward slash\) and press Enter\.

1. In screen **BANK70**:
   + In **The amount you would like to borrow\.\.\.:**, enter `10000`\.
   + In **At an interest rate of\.\.\.\.\.\.\.\.\.\.\.\.\.\.\.\.:**, enter `5.0`\.
   + In **For how many months\.\.\.\.\.\.\.\.\.\.\.\.\.\.\.\.\.\.\.:**, enter `10`\.
   + Press Enter\.

   The following result should be displayed:

   ```
         Resulting monthly payment.............:   $1023.06
   ```

   This completes the BANKDEMO application setup in Enterprise Developer\.

### Stop the BANKDEMO server from Enterprise Developer<a name="tutorial-cicd-mf-setup-test-stop-bankdemo"></a>

1. In **Server Explorer**, choose **Default**, then choose **Refresh** from the context menu\.

1. Choose **BANKDEMO**\.

1. From the context menu, choose **Stop**\.

   The Console view should display the log for the server stopping\.

   If the message `Server: BANKDEMO stopped successfully` is displayed, the server has successfully shut down\.

## Exercise 1: Enhance Loan Calculation in BANKDEMO Application<a name="tutorial-cicd-mf-exer-1"></a>

**Topics**
+ [Add loan analysis rule to Enterprise Developer Code Analysis](#tutorial-cicd-mf-exer-1-add-rule)
+ [Step 1: Perform code analysis for loan calculation](#tutorial-cicd-mf-exer-1-code-analysis)
+ [Step 2: Modify CICS BMS map and COBOL program and test](#tutorial-cicd-mf-exer-1-cics-bms)
+ [Step 3: Add total amount calculation in COBOL program](#tutorial-cicd-mf-exer-1-add-calculation)
+ [Step 4: Commit changes and run CI/CD pipeline](#tutorial-cicd-mf-exer-1-commit-changes)

In this scenario, you walk through the process of making a sample change to the code, deploying it, and testing it\.

The Loan department wants a new field on the Loan Calculation screen BANK70 to show the Total Loan Amount\. This requires a change of the BMS screen MBANK70\.CBL, adding a new field and the corresponding screen handling program SBANK70P\.CBL with related copybooks\. In addition, the loan calculation routine in BBANK70P\.CBL needs to be extended with the additional formula\.

To complete this exercise, make sure you complete the following prerequisites\.
+ Download [BANKDEMO\-exercise\.zip](https://d3lkpej5ajcpac.cloudfront.net/demo/mf/BANKDEMO-exercise.zip) to `D:\PhotonUser\My Files\Home Folder`\.
+ Extract the zip file to `D:\PhotonUser\My Files\Home Folder\BANKDEMO-exercise`\.
+ Create folder `D:\PhotonUser\My Files\Home Folder\AnalysisRules`\.
+ Copy the rules file `Loan+Calculation+Update.General-1.xml` from the `BANKDEMO-exercise` folder to `D:\PhotonUser\My Files\Home Folder\AnalysisRules`\.

**Note**  
Code changes in \*\.CBL and \*\.CPY are marked with EXER01 in column 1 \- 6 for this exercise\.

### Add loan analysis rule to Enterprise Developer Code Analysis<a name="tutorial-cicd-mf-exer-1-add-rule"></a>

Analysis rules defined in Micro Focus Enterprise Analyzer can be exported from Enterprise Analyzer and imported into Enterprise Developer to run same analysis rules across the sources in the Enterprise Developer project\.

1. Open `Window/Preferences/Micro Focus/COBOL/Code Analysis/Rules`\.

1. Choose **Edit\.\.\.** and enter the folder name `D:\PhotonUser\My Files\Home Folder\AnalysisRules` containing the rules file `Loan+Calculation+Update.General-1.xml`\.

1. Choose **Finish**\.

1. Choose **Apply**, then choose **Close**\.

1. From the BANKDEMO project context menu, choose **Code Analysis**\.

   You should see an entry for **Loan Calculation Update**\.

### Step 1: Perform code analysis for loan calculation<a name="tutorial-cicd-mf-exer-1-code-analysis"></a>

With the new analysis rule we want to identify the COBOL programs and lines of code in there that are matching the search patterns `*PAYMENT*`, `*LOAN*` and `*RATE*` in expressions, statements and variables\. This will help to navigate through the code and identify required code changes\.

1. From the BANKDEMO project context menu, choose **Code Analysis/Loan Calculation Update**\.

   This will run the search rule and list the results in a new tab called **Code Analysis**\. The analysis run is completed when the green progress bar at the bottom right disappears\.

   The **Code Analysis** tab should display an expanded list of `BBANK20P.CBL`, `BBANK70P.CBL` and `SBANK70P.CBL`, each listing the statements, expressions and variables matching the search patterns\.

   Looking at the result for `BBANK20P.CBL` there are only literals moved that have a match with search pattern\. So this program can be ignored\.

1. In the tab menu bar choose **\- Icon** to collapse all\.

1. Expand `SBANK70P.CBL` and select any lines in any order with a double\-click to see how this will open the source and highlight the line selected in source code\. You will also recognize that all identified source lines are marked\.

### Step 2: Modify CICS BMS map and COBOL program and test<a name="tutorial-cicd-mf-exer-1-cics-bms"></a>

First we will change the BMS map `MBANK70.BMS` and the screen handling program `SBANK70P.CBL` and copybook `CBANKDAT.CPY` to display the new field\. To avoid unnecessary coding in this exercise, modified source modules are available in the `D:\PhotonUser\My Files\Home Folder\BANKDEMO-exercise\Exercise01` folder\. Normally a developer would use the Code Analysis results to navigate and modify the sources\. If you have the time and and want to do the manual changes do so with the information provided in \*Manual change in MBANK70\.BMS and SBANK70P\.CBL \(Optional\)\*\.

For quick changes, copy the following files:

1. `..\BANKDEMO-exercise\Exercis01\screens\MBANK70.BMS` to `D:\PhotonUser\workspace\bankdemo\source\screens`\.

1. `.\BANKDEMO-exercise\Exercis01\cobol\SBANK70P.CBL` to `D:\PhotonUser\workspace\bankdemo\source\cobol`\.

1. `..\BANKDEMO-exercise\Exercis01\copybook\CBANKDAT.CPY` to `D:\PhotonUser\workspace\bankdemo\source\copybook`\.

1. To ensure that all programs impacted by the changes are compiled, choose **Project/Clean\.\.\./Clean all project**\.

For manual changes to `MBANK70.BMS` and `SBANK70P.CBL`, complete the following steps:
+ For manual change in BMS `MBANK70.BMS` source add after the `PAYMENT` field:
  + TXT09 with same attributes as TXT08 and INITIAL value “Total Loan Amount”
  + TOTAL with same attributes as PAYMENT

**Test changes**

To test the changes, repeat the steps in the following sections:

1. [Start the BANKDEMO server from Enterprise Developer](#tutorial-cicd-mf-setup-test-start-bankdemo)

1. [Start the Rumba 3270 terminal](#tutorial-cicd-mf-setup-test-start-3270)

1. [Run a BankDemo transaction](#tutorial-cicd-mf-setup-test-run-bankdemo)

   In addition you should now also see the text `Total Loan Amount.....................:`\.

1. [Stop the BANKDEMO server from Enterprise Developer](#tutorial-cicd-mf-setup-test-stop-bankdemo)

### Step 3: Add total amount calculation in COBOL program<a name="tutorial-cicd-mf-exer-1-add-calculation"></a>

In the second step we will change `BBANK70P.CBL` and add the calculation for the total loan amount\. The prepared source with required changes is available in `D:\PhotonUser\My Files\Home Folder\BANKDEMO-exercise\Exercise01` folder\. If you have the time and and want to do the manual changes do so with the information provided in \*Manual change in BBANK70P\.CBL \(Optional\)\*\.

For quick change, copy the following file:
+ `..\BANKDEMO-exercise\Exercis01\source\cobol\BBANK70P.CBL` to `D:\PhotonUser\workspace\bankdemo\source\cobol`\.

To make a manual change to `BBANK70P.CBL`, complete the following steps:
+ Use the Code Analysis result to identify the required changes\.

**Test changes**

To test the changes, repeat the steps in the following sections:

1. [Start the BANKDEMO server from Enterprise Developer](#tutorial-cicd-mf-setup-test-start-bankdemo)

1. [Start the Rumba 3270 terminal](#tutorial-cicd-mf-setup-test-start-3270)

1. [Run a BankDemo transaction](#tutorial-cicd-mf-setup-test-run-bankdemo)

   In addition you should now also see the text `Total Loan Amount.....................: $10230.60`\.

1. [Stop the BANKDEMO server from Enterprise Developer](#tutorial-cicd-mf-setup-test-stop-bankdemo)

### Step 4: Commit changes and run CI/CD pipeline<a name="tutorial-cicd-mf-exer-1-commit-changes"></a>

Commit the changes to the central CodeCommit repository and trigger the CI/CD pipeline to build, test, and deploy the changes\.

1. From BANKDEMO project, in the context menu, choose **Team/Commit**\.

1. In the **Git Staging** tab, enter the following commit message: `Added Total Amount Calculation`\.

1. Choose **Commit and Push\.\.\.**\.

1. Open the CodePipeline console and check status of the pipeline execution\.
**Note**  
In case you face any problem with the Enterprise Developer or Teams function Commit or Push, use the Git Bash command line interface\.

## Exercise 2: Extract loan calculation in BankDemo application<a name="tutorial-cicd-mf-exer-2"></a>

**Topics**
+ [Step 1: Refactor loan calculation routine into a COBOL section](#tutorial-cicd-mf-exer-2-extract-loan)
+ [Step 2: Extract loan calculation routine to a standalone COBOL program](#tutorial-cicd-mf-exer-2-standalone)
+ [Step 3: Commit changes and run the CI/CD pipeline](#tutorial-cicd-mf-exer-2-commit-run)

In this next exercise, you work through another sample change request\. In this scenario, the Loan department want to reuse the loan calculation routine as a standalone WebService\. The routine should remain in COBOL and should also still be callable from the existing CICS COBOL program `BBANK70P.CBL`\.

### Step 1: Refactor loan calculation routine into a COBOL section<a name="tutorial-cicd-mf-exer-2-extract-loan"></a>

In the first step we extract the loan calculation routine into a COBOL Section\. This step is required to extract the code into a stand\-alone COBOL program in the next step\.

1. Open `BBANK70P.CBL` in the COBOL Editor\.

1. In the editor, choose from the context menu **Code Analysis/Loan Calculation Update**\. This will only scan the current source for patterns defined in the analysis rule\.

1. In the result in the **Code Analysis** tab, find the first arithmetic statement `DIVIDE WS-LOAN-INTEREST BY 12`\.

1. Double click on the statement to navigate to source line in Editor\. This is the first statement of the loan calculation routine\.

1. Mark the following code block for loan calculation routine to be extracted to a section\.

   ```
              DIVIDE WS-LOAN-INTEREST BY 12
                GIVING WS-LOAN-INTEREST ROUNDED.
              COMPUTE WS-LOAN-MONTHLY-PAYMENT ROUNDED =
                ((WS-LOAN-INTEREST * ((1 + WS-LOAN-INTEREST)
                    ** WS-LOAN-TERM)) /
                (((1 + WS-LOAN-INTEREST) * WS-LOAN-TERM) - 1 ))
                    * WS-LOAN-PRINCIPAL.
   EXER01     COMPUTE WS-LOAN-TOTAL-PAYMENT =
   EXER01             (WS-LOAN-MONTHLY-PAYMENT * WS-LOAN-TERM).
   ```

1. From the context menu in the editor, choose **Refactor/Extract to Section\.\.\.**\.

1. Enter **New section name: LOAN\-CALCULATION**\.

1. Choose OK\.

   The marked code block has now been extracted to the new `LOAN-CALCULATION` section and the code block has been replaced with the `PERFROM LOAN-CALCULATION` statement\.

**Test changes**

To test the changes repeat the steps described in the following sections\.

1. [Start the BANKDEMO server from Enterprise Developer](#tutorial-cicd-mf-setup-test-start-bankdemo)

1. [Start the Rumba 3270 terminal](#tutorial-cicd-mf-setup-test-start-3270)

1. [Run a BankDemo transaction](#tutorial-cicd-mf-setup-test-run-bankdemo)

   In addition you should now also see the text `Total Loan Amount.....................: $10230.60`\.

1. [Stop the BANKDEMO server from Enterprise Developer](#tutorial-cicd-mf-setup-test-stop-bankdemo)
**Note**  
If you want to avoid the above steps to extract the code block to a section you can copy the modified source for Step 1 from `..\BANKDEMO-exercise\Exercis02\Step1\cobol\BBANK70P.CBL` to `D:\PhotonUser\workspace\bankdemo\source\cobol`\.

### Step 2: Extract loan calculation routine to a standalone COBOL program<a name="tutorial-cicd-mf-exer-2-standalone"></a>

In Step 2 the code block in the `LOAN-CALCULATION` section will be extracted to a standalone program and the original code will be replaced with code to call the new subprogram\.

1. Open `BBANK70P.CBL` in editor and find the new `PERFORM LOAN-CALCULATION` statement created in Step 1\.

1. Place the cursor within the section name\. It will be marked grey\.

1. From the context menu, select **Refactor\->Extract Section/Paragraph to Program\.\.\.**\.

1. In **Extract Section/Paragraph to Program**, enter **New file name: LOANCALC\.CBL**\.

1. Choose **OK**\.

   The new `LOANCALC.CBL` program will open in the editor\.

1. Scroll down and review the code being extracted and generated for the call interface\.

1. Select editor with `BBANK70P.CBL` and go to `LOAN-CALCULATION SECTION`\. Review the code being generated to call the new sub\-program `LOANCALC.CBL`\.
**Note**  
The `CALL` statement is using `DFHEIBLK` and `DFHCOMMAREA` to call `LOANCALC` with CICS control blocks\. Because we want to call the new `LOANCALC.CBL` sub\-program as non\-CICS program, we have to remove `DFHEIBLK` and `DFHCOMMAREA` from the call either by commenting out or deleting\.

**Test changes**

To test the changes repeat the steps described in the following sections\.

1. [Start the BANKDEMO server from Enterprise Developer](#tutorial-cicd-mf-setup-test-start-bankdemo)

1. [Start the Rumba 3270 terminal](#tutorial-cicd-mf-setup-test-start-3270)

1. [Run a BankDemo transaction](#tutorial-cicd-mf-setup-test-run-bankdemo)

   In addition you should now also see the text `Total Loan Amount.....................: $10230.60`\.

1. [Stop the BANKDEMO server from Enterprise Developer](#tutorial-cicd-mf-setup-test-stop-bankdemo)
**Note**  
If you want to avoid the above steps to extract the code block to a section you can copy the modified source for Step 1 from `..\BANKDEMO-exercise\Exercis02\Step2\cobol\BBANK70P.CBL` and `LOANCALC.CBL `to `D:\PhotonUser\workspace\bankdemo\source\cobol`\.

### Step 3: Commit changes and run the CI/CD pipeline<a name="tutorial-cicd-mf-exer-2-commit-run"></a>

****

Commit the changes to the central CodeCommit reposiotry and trigger the CI/CD Pipeline to build, test and deploy the changes\.

1. From BANKDEMO project, in the context menu, choose **Team/Commit**\.

1. In the **Git Staging** tab
   + Add in **Unstaged Stages LOANCALC\.CBL** and **LOANCALC\.CBL\.mfdirset**\.
   + Enter a commit message: `Added Total Amount Calculation`\.

1. Choose **Commit and Push\.\.\.**\.

1. Open the CodePipeline console and check status of the pipeline execution\.
**Note**  
In case you face any problem with the Enterprise Developer or Teams function Commit or Push, use the Git Bash command line interface\.

## Clean up resources<a name="tutorial-cicd-mf-clean"></a>

If you no longer need the resources you created for this tutorial, delete them so that you won't continue to be charged for them\. Complete the following steps:
+ Delete the CodePipeline pipeline\. For more information, see [Delete a pipeline in CodePipeline](https://docs.aws.amazon.com/codepipeline/latest/userguide/pipelines-delete.html) in the *AWS CodePipeline User Guide*\.
+ Delete the CodeCommit repository\. For more information, see [Delete an CodeCommit repository](https://docs.aws.amazon.com/codecommit/latest/userguide/how-to-delete-repository.html) in the *AWS CodeCommit User Guide*\.
+ Delete the S3; bucket\. For more information, see [Deleting a bucket](https://docs.aws.amazon.com/AmazonS3/latest/userguide/delete-bucket.html) in the *Amazon Simple Storage Service User Guide*\.
+ Delete the AWS CloudFormation stack\. For more information, see [Deleting a stack on the AWS CloudFormation console](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-console-delete-stack.html) in the *AWS CloudFormation User Guide*\.