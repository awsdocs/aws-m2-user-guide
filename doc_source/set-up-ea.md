# Tutorial: Set up Enterprise Analyzer on AppStream 2\.0<a name="set-up-ea"></a>

This tutorial describes how to set up Micro Focus Enterprise Analyzer to analyze one or more mainframe applications\. The Enterprise Analyzer tool provides several reports based on the analysis of the application source code and system definitions\.

This setup is designed to foster team collaboration while remaining simple to install: it uses an Amazon S3 bucket to share the source code via virtual disks \(leveraging [Rclone](https://rclone.org/)\) on the Windows machine and a common Amazon RDS instance \(running [PostgreSQL](https://www.postgresql.org/)\) allows any member of the team to access to all requested reports\.

Team members can also mount the virtual Amazon S3\-backed disk on their personal machines, which allows them to easily update the source bucket from their workstations\. They can potentially use scripts or any other form of automation running on their machines and connected to other on\-premise internal systems\.

The setup is based on the AppStream 2\.0 Windows images that AWS Mainframe Modernization shares with the customer and on the creation of AppStream 2\.0 fleets and stacks as described in [Tutorial: Set up AppStream 2\.0 for use with Micro Focus Enterprise Analyzer and Micro Focus Enterprise Developer](set-up-appstream.md)\.

**Important**  
The steps in this tutorial assume that you set up AppStream 2\.0 using the downloadable AWS CloudFormation template [cfn\-m2\-appstream\-fleet\-ea\-ed\.yaml](https://d1vi4vxke6c2hu.cloudfront.net/tutorial/cfn-m2-appstream-fleet-ea-ed.yaml)\. For more information, see [Tutorial: Set up AppStream 2\.0 for use with Micro Focus Enterprise Analyzer and Micro Focus Enterprise Developer](set-up-appstream.md)\.  
You must perform the steps of this tutorial when the Enterprise Analyzer fleet and stack are up and running\.

For a complete description of Enterprise Analyzer v7 features and deliverables, check out its [up\-to\-date online documentation \(v7\.0\)](https://www.microfocus.com/documentation/enterprise-analyzer/ea70/EA/GUID-ACFC46C4-1983-46B8-B911-DE3559193D7D.html) on the Micro Focus site\.

## Image contents<a name="set-up-ea-image-contents"></a>

In addition to Enterprise Analyzer itself, the image contains the following tools and libraries\.

Third\-party tools
+ [Python](https://www.python.org/)
+ [Rclone](https://rclone.org/)
+ [pgAdmin](https://www.pgadmin.org/)
+ [git\-scm](https://git-scm.com/)
+ [PostgreSQL ODBC driver](https://odbc.postgresql.org/)

Libraries in `C:\Public`
+ BankDemo source code and project definition for Enterprise Developer: `m2-bankdemo-template.zip`\.
+ MFA install package for the mainframe: `mfa.zip`\. For more information, see [Mainframe Access Overview](https://www.microfocus.com/documentation/enterprise-developer/30pu12/ED-VS2012/BKMMMMINTRS001.html) in the *Micro Focus Enterprise Developer *documentation\.
+ Command and config files for Rclone \(instructions for their use in the tutorials\): `m2-rclone.cmd` and `m2-rclone.conf`\.

**Topics**
+ [Image contents](#set-up-ea-image-contents)
+ [Prerequisites](#tutorial-ea-prerequisites)
+ [Step 1: Setup](#tutorial-ea-step1)
+ [Step 2: Create the Amazon S3\-based virtual disk on Windows](#tutorial-ea-step2)
+ [Step 3: Create an ODBC source for the Amazon RDS instance](#tutorial-ea-step3)
+ [Subsequent sessions](#tutorial-ea-step4)
+ [Troubleshooting workspace connection](#tutorial-ea-step5)
+ [Clean up resources](#tutorial-ea-clean)

## Prerequisites<a name="tutorial-ea-prerequisites"></a>
+ Load the source code and system definitions \(CICS CSD, DB2 object definitions, etc\.\) for the customer application to analyze in an Amazon S3 bucket with a folder structure that corresponds to the expected file system organization for the Enterprise Analyzer user on Windows\.
+ Create and start an Amazon RDS instance running PostgreSQL\. This instance will store the data and results produced by Enterprise Analyzer\. You can share this instance across all members of the application team\. In addition, create an empty schema called `m2_ea` \(or any other suitable name\) in the database and define credentials for authorized users that allow them to create, insert, update, and delete items in this schema\. You can obtain the database name, its server endpoint url and tcp port from the Amazon RDS console or the account administrator\.

## Step 1: Setup<a name="tutorial-ea-step1"></a>

1. Start a session with AppStream 2\.0 based on the url received in the welcome email\.

1. Use your email as your userid and define your permanent password\.

1. Select the Enterprise Analyzer stack\.

1. On the AppStream 2\.0 menu page, choose **Desktop** to reach the Windows desktop streamed by the fleet\.

## Step 2: Create the Amazon S3\-based virtual disk on Windows<a name="tutorial-ea-step2"></a>

**Note**  
If you already used `rclone` during the AWS Mainframe Modernization preview, you must update `run-rclone.cmd` to the newer version located in `C:\Users\Public`\.

1. Copy the `rclone.conf` and `run-rclone.cmd` files provided in `C:\Users\Public` to your home folder `C:\Users\PhotonUser\My Files\Home Folder` using File Explorer\. These files might already be present in this directory if you previously installed Enterprise Developer in your AppStream 2\.0 account\.

1. Update the `rclone.conf` config parameters with your Amazon S3 bucket name, your AWS access key and corresponding secret\.

1. Update `your-local-folder-path` to the path of the directory where you want the application files synced from the Amazon S3 bucket that contains them\. This synced directory must be a subdirectory of the Home Folder in order for AppStream 2\.0 to properly back up and restore it on session start and end\.

1. Open a Windows command prompt, cd to `C:\Users\Home Folder` if needed and run `run-rclone.cmd`\. When the command finishes, you should see the source code of the application located in Amazon S3 bucket in Windows File Explorer as disk `m2-s3` \(the name defined in the heading line of `rclone.conf`\.

To add new files to the set that you are working on or to update existing ones, upload the files to the Amazon S3 bucket and they will be synced to your directory at the next iteration defined in `run-rclone.cmd`\. Similarly, if you want to delete some files, delete them from the Amazon S3 bucket\. The next sync operation will delete them from your local directory\.

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

1. Start a session with AppStream 2\.0 based on the url received in the welcome email\.

1. Login with your email and permanent password\.

1. Select the Enterprise Analyzer stack\.

1. Launch `Rclone` to connect \(see above\) to the Amazon S3\-backed disk when this option is used to share the workspace files\.

1. Launch Enterprise Analyzer to do your work\.

## Troubleshooting workspace connection<a name="tutorial-ea-step5"></a>

When you try to reconnect to your Enterprise Analyzer workspace, you might see an error like the following:

```
Cannot access the workspace directory D:\PhotonUser\My Files\Home Folder\EA_BankDemo. The workspace has been created on a non-shared disk of the EC2AMAZ-E6LC33H computer. Would you like to correct the workspace directory location?
```

To resolve this issue, choose **OK** to clear the message, then complete the following steps\.

1. In AppStream 2\.0, choose the Launch Application icon on the toolbar then choose **EA\_Admin** to start the Micro Focus Enterprise Analyzer Administration tool\.  
![\[The AppStream 2.0 launch selector menu with the Micro Focus Enterprise Analyzer administration tool selected.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/aas-launch-selector.png)

1. On **Administer** choose **Refresh Workspace Path\.\.\.**\.  
![\[The Micro Focus Enterprise Analyzer administration tool administrator menu with Refresh Workspace Path selected.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/ea_admin-administer-refresh.png)

1. In **Select workspace**, choose the workspace you want, then choose **OK**\.  
![\[The Micro Focus Enterprise Analyzer administration tool Select workspace dialog box with a project selected.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/ea_admin-select-workspace.png)

1. Confirm the message by choosing **OK**\.  
![\[The Enterprise Analyzer Cannot access the workspace directory error message with OK selected.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/ea_admin-select-workspace-error.png)

1. In **Workspace directory network path** enter the correct path to your workspace; for example, `D:\PhotonUser\My Files\Home Folder\EA\MyWorkspace3`\.  
![\[The Enterprise Analyzer Workspace directory network path dialog box with an example path entered and OK selected.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/ea_admin-workspace-directory-network-path.png)

1. Close the Micro Focus Enterprise Analyzer Administration tool\.  
![\[The Micro Focus Enterprise Analyzer Administration tool with the Close button selected.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/ea_admin-close.png)

1. In AppStream 2\.0, choose the Launch Application icon on the toolbar then choose **EA** to start Micro Focus Enterprise Analyzer\.  
![\[The AppStream 2.0 launch application icon with EA selected.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/aas-launch-ea.png)

1. Repeat steps 3 \- 5\.

Micro Focus Enterprise Analyzer should now open with the existing workspace\.

## Clean up resources<a name="tutorial-ea-clean"></a>

If you no longer need the resources you created for this tutorial, delete them so that you won't continue to be charged for them\. Complete the following steps:
+ Use the **EA\_Admin** tool to delete the workspace\.
+ Delete the Amazon S3 buckets you created for this tutorial\. For more information, see [Deleting a bucket](https://docs.aws.amazon.com/AmazonS3/latest/userguide/delete-bucket.html) in the *Amazon S3 User Guide*\.
+ Delete the database you created for this tutorial\. For more information, see [Deleting a DB instance](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_GettingStarted.CreatingConnecting.PostgreSQL.html#CHAP_GettingStarted.Deleting.PostgreSQL)\.