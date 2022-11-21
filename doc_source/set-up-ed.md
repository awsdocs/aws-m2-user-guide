# Tutorial: Set up Micro Focus Enterprise Developer on AppStream 2\.0<a name="set-up-ed"></a>

This tutorial describes how to set up Micro Focus Enterprise Developer for one or more mainframe applications in order to maintain, compile, and test them using the Enterprise Developer features\. The setup is based on the AppStream 2\.0 Windows images that AWS Mainframe Modernization shares with the customer and on the creation of AppStream 2\.0 fleets and stacks as described in [Tutorial: Set up AppStream 2\.0 for use with Micro Focus Enterprise Analyzer and Micro Focus Enterprise Developer](set-up-appstream.md)\.

**Important**  
The steps in this tutorial assume that you set up AppStream 2\.0 using the downloadable AWS CloudFormation template [cfn\-m2\-appstream\-fleet\-ea\-ed\.yaml](https://d1vi4vxke6c2hu.cloudfront.net/tutorial/cfn-m2-appstream-fleet-ea-ed.yaml)\. For more information, see [Tutorial: Set up AppStream 2\.0 for use with Micro Focus Enterprise Analyzer and Micro Focus Enterprise Developer](set-up-appstream.md)\.  
You must perform the steps of this setup when the Enterprise Developer fleet and stack are up and running\.

For a complete description of Enterprise Developer v7 features and deliverables, check out its [up\-to\-date online documentation \(v7\.0\)](https://www.microfocus.com/documentation/enterprise-developer/ed70/ED-Eclipse/GUID-8D6B7358-AC35-4DAF-A445-607D8D97EBB2.html) on the Micro Focus site\.

## Image contents<a name="set-up-ed-image-contents"></a>

In addition to Enterprise Developer itself, the image contains the image contains Rumba \(a TN3270 emulator\)\. It also contains the following tools and libraries\.

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

If you need to access source code that is not yet loaded into CodeCommit repositories, but that is available in an Amazon S3 bucket, for example to perform the initial load of the source code into git, follow the procedure to create a virtual Windows disk as described in [Tutorial: Set up Enterprise Analyzer on AppStream 2\.0](set-up-ea.md)\.

**Topics**
+ [Image contents](#set-up-ed-image-contents)
+ [Prerequisites](#tutorial-ed-prerequisites)
+ [Step 1: Setup by individual Enterprise Developer users](#tutorial-ed-step1)
+ [Step 2: Create the Amazon S3\-based virtual folder on Windows \(optional\)](#tutorial-ed-step2)
+ [Step 3: Clone the repository](#tutorial-ed-step3)
+ [Subsequent sessions](#tutorial-ed-step4)
+ [Clean up resources](#tutorial-ed-clean)

## Prerequisites<a name="tutorial-ed-prerequisites"></a>
+ One or more CodeCommit repositories loaded with the source code of the application to be maintained\. The repository setup should match the requirements of the CI/CD pipeline above to create synergies by combination of both tools\.
+ IAM users with credentials to the CodeCommit repository or repositories defined by the account administrator for each application teammate according to the information in [Authentication and access control for AWS CodeCommit](https://docs.aws.amazon.com/codecommit/latest/userguide/auth-and-access-control.html)\. The structure of those credentials is reviewed in [Authentication and access control for AWS CodeCommit](https://docs.aws.amazon.com/codecommit/latest/userguide/auth-and-access-control.html) and the complete reference for IAM authorizations for CodeCommit is in the [CodeCommit permissions reference](https://docs.aws.amazon.com/codecommit/latest/userguide/auth-and-access-control-permissions-reference.html): the administrator may define distinct IAM policies for distinct roles having credentials specific to the role for each repository and limiting its authorizations of the user to the specific set of tasks that he has to to accomplish on a given repository\. So, for each maintainer of the CodeCommit repository, the account administrator will generate a primary IAM user, grant this user permissions to access the required repository or repositories via selection proper IAM policy or policies for CodeCommit access\.

## Step 1: Setup by individual Enterprise Developer users<a name="tutorial-ed-step1"></a>

1. Obtain your IAM credentials:

   1. Connect to the AWS console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

   1. Follow the procedure described in step 3 of [Setup for HTTPS users using Git credentials](https://docs.aws.amazon.com/codecommit/latest/userguide/setting-up-gc.html) in the *AWS CodeCommit User Guide*\. 

   1. Copy the CodeCommit\-specific user name and password that IAM generated for you, either by showing, copying, and then pasting this information into a secure file on your local computer, or by choosing **Download credentials** to download this information as a \.CSV file\. You need this information to connect to CodeCommit\.

1. Start a session with AppStream 2\.0 based on the url received in the welcome email\. Use your email as user name and create your password\.

1. Select your Enterprise Developer stack\.

1. On the menu page, choose **Desktop** to reach the Windows desktop streamed by the fleet\.

## Step 2: Create the Amazon S3\-based virtual folder on Windows \(optional\)<a name="tutorial-ed-step2"></a>

If there is a need for Rclone \(see above\), create the Amazon S3\-based virtual folder on Windows: \(optional if all application artefacts exclusively come from CodeCommit access\)\.

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

## Step 3: Clone the repository<a name="tutorial-ed-step3"></a>

1. Navigate to the application selector menu in the top left corner of the browser window and select Enterprise Developer\.

1. Complete the workspace creation required by Enterprise Developer in your Home folder by choosing `C:\Users\PhotonUser\My Files\Home Folder` \(aka `D: \PhotonUser\My Files\Home Folder`\) as location for the workspace\.

1. In Enterprise Developer, clone your CodeCommit repository by going to the Project Explorer, right click and choose **Import**, **Import …**, **Git**, **Projects** from **Git** **Clone URI**\. Then, enter your CodeCommit\-specific user name and password and complete the Eclipse dialog to import the code\.

The CodeCommit git repository in now cloned in your local workspace\.

Your Enterprise Developer workspace is now ready to start the maintenance work on your application\. In particular, you can use the local instance of Microfocus Enterprise Server \(ES\) integrated with Enterprise Developer to interactively debug and run your application to validate your changes locally\.

**Note**  
The local Enterprise Developer environment, including the local Enterprise Server instance, runs under Windows while AWS Mainframe Modernization runs under Linux\. We recommend that you run complementary tests in the Linux environment provided by AWS Mainframe Modernization after you commit the new application to CodeCommit and rebuild it for this target and before you roll out the new application to production\.

## Subsequent sessions<a name="tutorial-ed-step4"></a>

As you select a folder that is under AppStream 2\.0 management like the home folder for the cloning of your CodeCommit repository, it will be saved and restored transparently across sessions\. Complete the following steps the next time you need to work with the application: 

1. Start a session with AppStream 2\.0 based on the url received in the welcome email\.

1. Login with your email and permanent password\.

1. Select the Enterprise Developer stack\.

1. Launch `Rclone` to connect \(see above\) to the Amazon S3\-backed disk when this option is used to share the workspace files\.

1. Launch Enterprise Developer to do your work\.

## Clean up resources<a name="tutorial-ed-clean"></a>

If you no longer need the resources you created for this tutorial, delete them so that you won't continue to be charged for them\. Complete the following steps:
+ Delete the CodeCommit reponsitory you created for this tutorial\. For more information, see [Delete an CodeCommit repository](https://docs.aws.amazon.com/codecommit/latest/userguide/how-to-delete-repository.html) in the *AWS CodeCommit User Guide*\.
+ Delete the IAM users you set up for this tutorial\. For more information, see [Deleting an IAM user](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_manage.html#id_users_deleting) in the *IAM User Guide*\.
+ Delete the database you created for this tutorial\. For more information, see [Deleting a DB instance](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_GettingStarted.CreatingConnecting.PostgreSQL.html#CHAP_GettingStarted.Deleting.PostgreSQL)\.