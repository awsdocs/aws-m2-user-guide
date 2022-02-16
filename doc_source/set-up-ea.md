# Setting up Enterprise Analyzer on Amazon AppStream<a name="set-up-ea"></a>

This document describes how to set up Micro Focus Enterprise Analyzer to analyze one or more mainframe applications\. The Enterprise Analyzer tool provides several reports based on the analysis of the application source code and system definitions\.

This setup is designed to foster team collaboration while remaining simple to install: an Amazon S3 bucket is used to share the source code via virtual disks \(leveraging Rclone\) on the Windows machine and a common Amazon RDS instance \(running PostgreSQL\) allows access to all requested reports by any member of the team\.

The virtual Amazon S3\-backed disk can also be mounted on personal machines of team members to allow them to easily update the source bucket from their workstation, potentially by scripts or any other form of automation running there and connected to other on\-premise internal systems\.

The setup is based on the Amazon AppStream Windows images shared by AWS Mainframe Modernization with the customer and on the creation of Amazon AppStream fleets and stacks as described in [Setting up Amazon AppStream for use with AWS Mainframe Modernization Enterprise Analyzer and Enterprise Developer](set-up-appstream.md)\.

**Important**  
You must perform the steps of this setup when the Enterprise Analyzer fleet and stack are up and running\.

For a complete description of Enterprise Analyzer v7 features and deliverables, check out its [up\-to\-date online documentation \(v7\.0\)](https://www.microfocus.com/documentation/enterprise-analyzer/ea70/EA/GUID-ACFC46C4-1983-46B8-B911-DE3559193D7D.html) on the Micro Focus site\.

In addition to Enterprise Analyzer itself, the image also offers [pgAdmin](https://www.pgadmin.org/), the feature\-rich database administration and development platform for PostgreSQL, for direct access to the Enterprise Analyzer Amazon RDS database, if needed\. Finally, git\-scm tools are also installed to allow for efficient interactions with git repositories \(such as AWS CodeCommit\), potentially used to store application source code in the context of the global migration / transformation project\.

If you want to explore the service without using your own code, you can use the source code, sample data and system definitions of the sample BankDemo application, which are available in the `C.\Users\Public\BankDemo` folder of the Amazon AppStream Windows instance\.

**Topics**
+ [Prerequisites](#tutorial-ea-prerequisites)
+ [Step 1: Setup](#tutorial-ea-step1)
+ [Step 2: Create the Amazon S3\-based virtual disk on Windows](#tutorial-ea-step2)
+ [Step 3: Create an ODBC source for the Amazon RDS instance](#tutorial-ea-step3)
+ [Subsequent sessions](#tutorial-ea-step4)

## Prerequisites<a name="tutorial-ea-prerequisites"></a>
+ Load the source code and system definitions \(CICS CSD, DB2 object definitions, etc\.\) for the customer application to analyze in an Amazon S3 bucket with a folder structure that corresponds to the expected file system organization for the Enterprise Analyzer user on Windows\.
+ Create and start an Amazon RDS instance running PostgreSQL v11\. This instance will store the data and results produced by Enterprise Analyzer\. You can share this instance across all members of the application team\. In addition, create an empty schema called `m2_ea` \(or any other suitable name\) in the database and define credentials for authorized users that allow them to create, insert, update, and delete items in this schema\. You can obtain the database name, its server endpoint url and tcp port from the Amazon RDS console or the account administrator\.

## Step 1: Setup<a name="tutorial-ea-step1"></a>

1. Start a session with Amazon AppStream based on the url received in the welcome email\.

1. Use your email as your userid and define your permanent password\.

1. Select the Enterprise Analyzer stack\.

1. On the Amazon AppStream menu page, choose **Desktop** to reach the Windows desktop streamed by the fleet\.

## Step 2: Create the Amazon S3\-based virtual disk on Windows<a name="tutorial-ea-step2"></a>

1. Copy the `rclone.conf` file provided in `C:\Users\Public` to your home folder `C:\Users\PhotonUser\My Files\Home Folder` using File Explorer\. The file might already be present in this directory if you previously installed Enterprise Developer in your Amazon AppStream account\.

1. Update its config parameters with your Amazon S3 bucket name, your AWS access key and corresponding secret\.

1. Copy the file `run-rclone.cmd` provided in `C:\Users\Public` to your home folder using File Explorer\.

1. Open a Windows command prompt, cd to `C:\Users\Home Folder` if needed and run `run-rclone.cmd`\. When the command finishes, you should see the source code of the application located in Amazon S3 bucket in Windows File Explorer as disk `m2-s3` \(the name defined in the heading line of `rclone.conf`\.

## Step 3: Create an ODBC source for the Amazon RDS instance<a name="tutorial-ea-step3"></a>

1. Navigate to the application selector menu in the top left corner of the browser window and choose **EA\_Admin** to start this tool\.

1. On the **Administer** menu, choose **ODBC Data Sources**, and choose **Add** from the **User DSN** tab\.

1. Choose the **PostgreSQL Unicode** driver, then choose **Finish**\.

1. On **PostgreSQL Unicode ODBC Driver \(psqlODBC\) Setup**, define the data source name that you want \(and take note of it\) and complete the following parameters with the values obtained above from the RDS instance you created:
   + Description
   + Database \(the Amazon RDS database\)
   + Server \(the Amazon RDS endpoint\)
   + Port \(The Amazon RDS port\)
   + User Name \(as defined in the Amazon RDS instance\)
   + Password \(as defined in the Amazon RDS instance\)\.

1. Choose **Test** to validate that the connection to Amazon RDS is successful, then choose **Save** to save your newly created User DSN\.

1. Choose **Ok** to finish with ODBC Data Sources and close the EA\_Admin tool\. Wait until you see the message confirming creation of the proper workspace\.

1. Navigate again to the application selector menu and choose Enterprise Analyzer to start it\.

1. In the Workspace configuration window, enter your workspace name and define its location\. It can be the Amazon S3\-based disk if you work under this config or your home folder if preferred\.

1. Choose **Choose Other Database** to connect to your Amazon RDS instance\.

1. Choose the **Postgre** icon from the options and choose **ok**\.

1. On Windows **Options – Define Connection Parameters**, enter the name of the data source that you created\. Also enter the database name, the schema name, the user name and password\. Choose **Ok**\.

1. Enterprise Analyzer will create all the tables, indexes, etc\. that it needs to store results\. This process might take a couple of minutes\. Enterprise Analyzer will confirm when the database and workspace are ready for use\.

1. Navigate again to the application selector menu and choose Enterprise Analyzer to start it\.

1. Enterprise Analyzer will display its startup window where the created workspace is now present and selected\. Choose **Ok**\.

1. Navigate to your repository in the left pane, right click and choose **Add files / folders to your workspace**, select the folder where your application code is stored to add it \(see above if you want to use BankDemo initially\)\. Enterprise Analyzer will then prompt you to verify those files, choose **Verify** to start the initial Enterprise Analyzer verification report\. It might take some minutes to complete, depending on the size of your application\.

1. The files and folders that you’ve added to your workspace should now be visible when you expand it\. Also, the object types and cyclomatic complexity reports will be visible in the top quadrant of the **Chart Viewer** pane\.

Enterprise Analyzer can now be used for all needed tasks\.

## Subsequent sessions<a name="tutorial-ea-step4"></a>

1. Start a session with Amazon AppStream based on the url received in the welcome email\.

1. Login with your email and permanent password\.

1. Select the Enterprise Analyzer stack\.

1. Launch `Rclone` to connect \(see above\) to the Amazon S3\-backed disk when this option is used to share the workspace files\.

1. Launch Enterprise Analyzer to do your work\.