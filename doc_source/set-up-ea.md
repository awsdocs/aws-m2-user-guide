# Tutorial: Set up Enterprise Analyzer on AppStream 2\.0<a name="set-up-ea"></a>

This tutorial describes how to set up Micro Focus Enterprise Analyzer to analyze one or more mainframe applications\. The Enterprise Analyzer tool provides several reports based on its analysis of the application source code and system definitions\.

This setup is designed to foster team collaboration\. Installation uses an Amazon S3 bucket to share the source code with virtual disks\. Doing this makes use of [Rclone](https://rclone.org/)\) on the Windows machine\. With a common Amazon RDS instance running [PostgreSQL](https://www.postgresql.org/) , any member of the team can access to all requested reports\.

Team members can also mount the virtual Amazon S3 backed disk on their personal machines\. and update the source bucket from their workstations\. They can potentially use scripts or any other form of automation on their machines if they are connected to other on\-premises internal systems\.

The setup is based on the AppStream 2\.0 Windows images that AWS Mainframe Modernization shares with the customer \. Setup is also based on the creation of AppStream 2\.0 fleets and stacks as described in [Tutorial: Set up AppStream 2\.0 for use with Micro Focus Enterprise Analyzer and Micro Focus Enterprise Developer](set-up-appstream.md)\.

**Important**  
The steps in this tutorial assume that you set up AppStream 2\.0 with the downloadable AWS CloudFormation template [cfn\-m2\-appstream\-fleet\-ea\-ed\.yml](https://drm0z31ua8gi7.cloudfront.net/tutorials/mf/appstream/cfn-m2-appstream-fleet-ea-ed.yml)\. For more information, see [Tutorial: Set up AppStream 2\.0 for use with Micro Focus Enterprise Analyzer and Micro Focus Enterprise Developer](set-up-appstream.md)\.  
To perform the steps in this tutorial, you must have set up your Enterprise Analyzer fleet and stack and they must be running\.

For a complete description of Enterprise Analyzer v7 features and deliverables, see its [up\-to\-date online documentation \(v7\.0\)](https://www.microfocus.com/documentation/enterprise-analyzer/ea70/EA/GUID-ACFC46C4-1983-46B8-B911-DE3559193D7D.html) on the Micro Focus site\.

## Image contents<a name="set-up-ea-image-contents"></a>

In addition to Enterprise Analyzer application itself, the image contains the following tools and libraries\.

Third\-party tools
+ [Python](https://www.python.org/)
+ [Rclone](https://rclone.org/)
+ [pgAdmin](https://www.pgadmin.org/)
+ [git\-scm](https://git-scm.com/)
+ [PostgreSQL ODBC driver](https://odbc.postgresql.org/)

Libraries in `C:\Users\Public`
+ BankDemo source code and project definition for Enterprise Developer: `m2-bankdemo-template.zip`\.
+ MFA install package for the mainframe: `mfa.zip`\. For more information, see [Mainframe Access Overview](https://www.microfocus.com/documentation/enterprise-developer/30pu12/ED-VS2012/BKMMMMINTRS001.html) in the *Micro Focus Enterprise Developer *documentation\.
+ Command and config files for Rclone \(instructions for their use in the tutorials\): `m2-rclone.cmd` and `m2-rclone.conf`\.

**Topics**
+ [Image contents](#set-up-ea-image-contents)
+ [Prerequisites](#tutorial-ea-prerequisites)
+ [Step 1: Setup](#tutorial-ea-step1)
+ [Step 2: Create the Amazon S3 based virtual folder on Windows](#tutorial-ea-step2)
+ [Step 3: Create an ODBC source for the Amazon RDS instance](#tutorial-ea-step3)
+ [Subsequent sessions](#tutorial-ea-step4)
+ [Troubleshooting workspace connection](#tutorial-ea-step5)
+ [Clean up resources](#tutorial-ea-clean)

## Prerequisites<a name="tutorial-ea-prerequisites"></a>
+ Upload the source code and system definitions for the customer application that you want to analyze to an S3 bucket\. The system definitions include CICS CSD, DB2 object definitions, and so on\. You can create a folder structure within the bucket that makes sense for how you want to organize the application artifacts\. For example, when you unzip the BankDemo sample, it has the following structure:

  ```
       demo
       |--> jcl
       |--> RDEF
       |--> transaction
       |--> xa
  ```
+ Create and start an Amazon RDS instance running PostgreSQL\. This instance will store the data and results produced by Enterprise Analyzer\. You can share this instance with all members of the application team\. In addition, create an empty schema called `m2_ea` \(or any other suitable name\) in the database\. Define credentials for authorized users that allow them to create, insert, update, and delete items in this schema\. You can obtain the database name, its server endpoint URL, and TCP port from the Amazon RDS console or from the account administrator\.
+ Make sure you have set up programmatic access to your AWS account\. For more information, see [Programmatic access](https://docs.aws.amazon.com/general/latest/gr/aws-sec-cred-types.html#access-keys-and-secret-access-keys) in the *Amazon Web Services General Reference\.*

## Step 1: Setup<a name="tutorial-ea-step1"></a>

1. Start a session with AppStream 2\.0 with the URL that you received in the welcome email message from AppStream 2\.0\.

1. Use your email as your user ID, and define your permanent password\.

1. Select the Enterprise Analyzer stack\.

1. On the AppStream 2\.0 menu page, choose **Desktop** to reach the Windows desktop that the fleet is streaming\.

## Step 2: Create the Amazon S3 based virtual folder on Windows<a name="tutorial-ea-step2"></a>

**Note**  
If you already used Rclone during the AWS Mainframe Modernization preview, you must update `m2-rclone.cmd` to the newer version located in `C:\Users\Public`\.

1. Copy the `m2-rclone.conf` and `m2-rclone.cmd` files provided in `C:\Users\Public` to your home folder `C:\Users\PhotonUser\My Files\Home Folder` using File Explorer\.

1. Update the `m2-rclone.conf` config parameters with your AWS access key and corresponding secret, as well as your AWS Region\.

   ```
   [m2-s3]
   type = s3
   provider = AWS
   access_key_id = YOUR-ACCESS-KEY
   secret_access_key = YOUR-SECRET-KEY
   region = YOUR-REGION
   acl = private
   server_side_encryption = AES256
   ```

1. In `m2-rclone.cmd`, make the following changes:
   + Change `your-s3-bucket` to your Amazon S3 bucket name\. For example, `m2-s3-mybucket`\.
   + Change `your-s3-folder-key` to your Amazon S3 bucket key\. For example, `myProject`\.
   + Change `your-local-folder-path` to the path of the directory where you want the application files synced from the Amazon S3 bucket that contains them\. For example, `D:\PhotonUser\My Files\Home Folder\m2-new`\. This synced directory must be a subdirectory of the Home Folder in order for AppStream 2\.0 to properly back up and restore it on session start and end\.

   ```
   :loop
   timeout /T 10
   "C:\Program Files\rclone\rclone.exe" sync m2-s3:your-s3-bucket/your-s3-folder-key "D:\PhotonUser\My Files\Home Folder\your-local-folder-path" --config "D:\PhotonUser\My Files\Home Folder\m2-rclone.conf"
   goto :loop
   ```

1. Open a Windows command prompt, cd to `C:\Users\PhotonUser\My Files\Home Folder` if needed and run `m2-rclone.cmd`\. This command script runs a continuous loop, syncing your Amazon S3 bucket and key to the local folder every 10 seconds\. You can adjust the time out as needed\. You should see the source code of the application located in the Amazon S3 bucket in Windows File Explorer\.

To add new files to the set that you are working on or to update existing ones, upload the files to the Amazon S3 bucket and they will be synced to your directory at the next iteration defined in `m2-rclone.cmd`\. Similarly, if you want to delete some files, delete them from the Amazon S3 bucket\. The next sync operation will delete them from your local directory\.

## Step 3: Create an ODBC source for the Amazon RDS instance<a name="tutorial-ea-step3"></a>

1. To start the EA\_Admin tool, navigate to the application selector menu in the top left corner of the browser window and choose **MF EA\_Admin**\.

1. From the **Administer** menu, choose **ODBC Data Sources**, and choose **Add** from the **User DSN** tab\.

1. In the Create New Data Source dialog box, choose the **PostgreSQL Unicode** driver, and then choose **Finish**\.

1. In the **PostgreSQL Unicode ODBC Driver \(psqlODBC\) Setup** dialog box, define and take note of the data source name that you want\. Complete the following parameters with the values from the RDS instance that you previously created:  
**Description**  
Optional description to help you identify this database connection quickly\.  
**Database**  
The Amazon RDS database you created previously\.  
**Server**  
The Amazon RDS endpoint\.  
**Port**  
The Amazon RDS port\.  
**User Name**  
As defined in the Amazon RDS instance\.  
**Password**  
As defined in the Amazon RDS instance\.

1. Choose **Test** to validate that the connection to Amazon RDS is successful, and then choose **Save** to save your new User DSN\.

1. Wait until you see the message that confirms creation of the proper workspace, and then choose **Ok** to finish with ODBC Data Sources and close the EA\_Admin tool\.

1. Navigate again to the application selector menu, and choose Enterprise Analyzer to start the tool\. Choose **Create New**\. 

1. In the Workspace configuration window, enter your workspace name and define its location\. The workspace can be the Amazon S3 based disk if you work under this config, or your home folder if you prefer\.

1. Choose **Choose Other Database** to connect to your Amazon RDS instance\.

1. Choose the **Postgre** icon from the options, and then choose **ok**\.

1. For the Windows settings under **Options – Define Connection Parameters**, enter the name of the data source that you created\. Also enter the database name, the schema name, the user name, and password\. Choose **Ok**\.

1. Wait for Enterprise Analyzer to create all the tables, indexes, and so on that it needs to store results\. This process might take a couple of minutes\. Enterprise Analyzer confirms when the database and workspace are ready for use\.

1. Navigate again to the application selector menu and choose Enterprise Analyzer to start the tool\.

1. The Enterprise Analyzer startup window appears in the new, selected workspace location\. Choose **Ok**\.

1. Navigate to your repository in the left pane, select the repository name, and choose **Add files / folders to your workspace**\.Select the folder where your application code is stored to add it to the workspace\. You can use the previous BankDemo example code if you want\. When Enterprise Analyzer prompts you to verify those files, choose **Verify** to start the initial Enterprise Analyzer verification report\. It might take some minutes to complete, depending on the size of your application\.

1. Expand your workspace to see the files and folders that you’ve added to the workspace\. The object types and cyclomatic complexity reports are also visible in the top quadrant of the **Chart Viewer** pane\.

You can now use Enterprise Analyzer for all needed tasks\.

## Subsequent sessions<a name="tutorial-ea-step4"></a>

1. Start a session with AppStream 2\.0 with the URL that you received in the welcome email message from AppStream 2\.0\.\.

1. Log in with your email and permanent password\.

1. Select the Enterprise Analyzer stack\.

1. Launch `Rclone` to connect to the Amazon S3 backed disk if you use this option to share the workspace files\.

1. Launch Enterprise Analyzer to do your work\.

## Troubleshooting workspace connection<a name="tutorial-ea-step5"></a>

When you try to reconnect to your Enterprise Analyzer workspace, you might see an error like this:

```
Cannot access the workspace directory D:\PhotonUser\My Files\Home Folder\EA_BankDemo. The workspace has been created on a non-shared disk of the EC2AMAZ-E6LC33H computer. Would you like to correct the workspace directory location?
```

To resolve this issue, choose **OK** to clear the message, and then complete the following steps\.

1. In AppStream 2\.0, choose the Launch Application icon on the toolbar, and then choose **EA\_Admin** to start the Micro Focus Enterprise Analyzer Administration tool\.  
![\[The AppStream 2.0 launch selector menu with the Micro Focus Enterprise Analyzer administration tool selected.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/aas-launch-selector.png)

1. From the **Administer** menu, choose **Refresh Workspace Path\.\.\.**\.  
![\[Administer menu of Micro Focus Enterprise Analyzer administration tool with Refresh Workspace Path selected.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/ea_admin-administer-refresh.png)

1. Under **Select workspace**, choose the workspace that you want, and then choose **OK**\.  
![\[The Select workspace dialog box of Micro Focus Enterprise Analyzer administration tool with a project selected.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/ea_admin-select-workspace.png)

1. Choose **OK** to confirm the error message\.  
![\[The Enterprise Analyzer error message Cannot access the workspace directory with OK selected.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/ea_admin-select-workspace-error.png)

1. Under **Workspace directory network path**, enter the correct path to your workspace, for example, `D:\PhotonUser\My Files\Home Folder\EA\MyWorkspace3`\.  
![\[The Enterprise Analyzer dialog box Workspace directory network path with an example path entered and OK selected.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/ea_admin-workspace-directory-network-path.png)

1. Close the Micro Focus Enterprise Analyzer Administration tool\.  
![\[The Micro Focus Enterprise Analyzer Administration tool with the Close button selected.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/ea_admin-close.png)

1. In AppStream 2\.0, choose the Launch Application icon on the toolbar, and then choose **EA** to start Micro Focus Enterprise Analyzer\.  
![\[The AppStream 2.0 launch application icon with EA selected.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/aas-launch-ea.png)

1. Repeat steps 3 \- 5\.

Micro Focus Enterprise Analyzer should now open with the existing workspace\.

## Clean up resources<a name="tutorial-ea-clean"></a>

If you no longer need the resources that you created for this tutorial, delete them so that you don't incur further charges\. Complete the following steps:
+ Use the **EA\_Admin** tool to delete the workspace\.
+ Delete the S3 buckets that you created for this tutorial\. For more information, see [Deleting a bucket](https://docs.aws.amazon.com/AmazonS3/latest/userguide/delete-bucket.html) in the *Amazon S3 User Guide*\.
+ Delete the database that you created for this tutorial\. For more information, see [Deleting a DB instance](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_GettingStarted.CreatingConnecting.PostgreSQL.html#CHAP_GettingStarted.Deleting.PostgreSQL)\.