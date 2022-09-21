# Tutorial: Use Blu Age Developer on AppStream 2\.0<a name="tutorial-ba-developer"></a>

This tutorial shows you how to access Blu Age Developer on AppStream 2\.0 and use it with a sample application so you can try out the features\. When you finish this tutorial, you can try using the same steps with your own applications\.

**Topics**
+ [Step 1: Create a database](#tutorial-ba-developer-create-db)
+ [Step 2: Access the environment](#tutorial-ba-developer-access-env)
+ [Step 3: Set up the runtime](#tutorial-ba-developer-set-up-runtime)
+ [Step 4: Start the Eclipse IDE](#tutorial-ba-developer-start-eclipse)
+ [Step 5: Set up a Maven project](#tutorial-ba-developer-set-up-maven)
+ [Step 6: Configure a Tomcat server](#tutorial-ba-developer-config-tomcat)
+ [Step 7: Deploy to Tomcat](#tutorial-ba-developer-deploy-tomcat)
+ [Step 8: Create the JICS database](#tutorial-ba-developer-create-jics)
+ [Step 9: Start and test the application](#tutorial-ba-developer-test-app)
+ [Step 10: Debug the application](#tutorial-ba-developer-debug)
+ [Clean up resources](#tutorial-ba-developer-clean)

## Step 1: Create a database<a name="tutorial-ba-developer-create-db"></a>

In this step, you use Amazon RDS to create a managed PostgreSQL database that the demo application uses to store configuration information\.

1. Open the Amazon RDS console\.

1. Choose **Databases > Create database**\.

1. Choose **Standard create > PostgreSQL**, leave default version, and choose **Free tier**\.

1. Choose a DB instance identifier and a master password \(or auto generate one\)\.

1. Ensure the VPC is the same as the AppStream 2\.0 instance one \(ask your administrator\)\.

1. For **VPC security group**, choose **Create New**\.

1. Leave **Public access** set to **No**\.

1. Leave \(and review\) all other default values\.

1. Choose **Create database**\.

After the database server is created, choose **View connection details** and take note of the master password and endpoint\.

Then, to make the database server accessible from your instance, select the database server in Amazon RDS, and, under **Connectivity & security**, choose its VPC security group \(which was previously created for you, and should have a description similar to **Created by RDS management console**\)\. Choose **Action > Edit inbound rules**, **Add rule**, and create a rule of type **PostgreSQL**\. For its source, use the security group **default** \(start typing it in the **Source** field, and accept the suggested ID\)\. Finally, choose **Save rules**\. 

## Step 2: Access the environment<a name="tutorial-ba-developer-access-env"></a>

In this step, you access the Blu Age development environment on AppStream 2\.0\.

1. Reach out to your administrator for the proper way to access your AppStream 2\.0 instance\. For general information about possible clients and configuration, see [AppStream 2\.0 Access Methods and Clients](https://docs.aws.amazon.com/appstream2/latest/developerguide/clients-access-methods-user.html) in the *Amazon AppStream 2\.0 Administration Guide*\. Consider using the native client for the best experience\.

1. In AppStream 2\.0 choose **Desktop View**\.

## Step 3: Set up the runtime<a name="tutorial-ba-developer-set-up-runtime"></a>

In this step, you set up the Blu Age runtime\. You must set up the runtime at first launch and again if you are notified of a runtime upgrade\. This step populates your `.m2` folder\.

1. In **Applications**, choose **Terminal**\.

1. Enter the following command:

   ```
      ~/_install-velocity-runtime.sh
   ```

## Step 4: Start the Eclipse IDE<a name="tutorial-ba-developer-start-eclipse"></a>

In this step, you start the Eclipse IDE and choose a location where you want to create a workspace\. 

1. In AppStream 2\.0 choose the Launch Application icon on the toolbar, then choose **Eclipse JEE**\.  
![\[The Launch Application icon on the toolbar in AppStream 2.0. Eclipse JEE is selected.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/aas-ba-eclipse.png)

1. When the launcher opens, enter the location where you want to create your workspace, and choose **Launch**\.  
![\[The Blu Age Eclipse IDE launcher in AppStream 2.0. Workspace is selected.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/ba-eclipse-launcher.png)

Optionally, you can launch Eclipse from the command line, as follows:

```
   ~/eclipse &
```

## Step 5: Set up a Maven project<a name="tutorial-ba-developer-set-up-maven"></a>

In this step, you import a Maven project for the Planets demo application\.

1. Upload [PlanetsDemo\-pom\.zip](https://d3lkpej5ajcpac.cloudfront.net/appstream/bluage/developer-ide/PlanetsDemo/PlanetsDemo-pom.zip) to your Home folder \(you can use the native client “My Files” feature for this\)\.

1. Use the `unzip` command line tool to extract the files\.

1. Navigate inside the unzipped folder and open the root `pom.xml` of your project in a text editor\.

1. Edit the `gapwalk.version` property so that it matches the installed Blu Age runtime\.

   If you are unsure of the installed version, issue the following command in a terminal:

   ```
      cat ~/runtime-version.txt
   ```

   This command will print the currently available runtime version; for example, `3.1.0-b3257-dev`\.
**Note**  
Do not include the `-dev` suffix in `gapwalk.version`, so for example a valid value would be `<gapwalk.version>3.1.0-b3257</gapwalk.version>`\.

1. In Eclipse, choose **File**, then **Import**\. In **Import**, expand **Maven** and choose **Existing Maven Projects** and choose **Next**\. 

1. In **Import Maven Projects**, provide the location of the extracted files and choose **Finish**\.

Eclipse will immediately start downloading required Maven dependencies and building the project\.

You can safely ignore the following popup, because Maven will download a local copy of node\.js to build the Angular \(\*\-web\) part of the project:

![\[Warning message about missing node.js.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/ba-eclipse-node-warning.png)

Wait until the end of the build, which you can follow in the **Progress** View\.

## Step 6: Configure a Tomcat server<a name="tutorial-ba-developer-config-tomcat"></a>

In this step, you configure a Tomcat server where you will deploy and start your compiled application\.

1. In Eclipse, choose **Window > Show View > Servers** to get the focus on the **Servers** view:  
![\[Blu Age Eclipse with Servers view selected.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/ba-eclipse-servers.png)

1. Choose **No servers are available\. Click this link to create a new server\.\.\.**\. The **New Server** Wizard pops up\. There, enter **tomcat v9** in the **Select the server type** field, and choose **Tomcat v9\.0 Server**:  
![\[The New Server dialog box. Tomcat v9.0 Server is selected.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/ba-eclipse-new-server.png)

1. Choose **Next**, then **Browse** and choose the **tomcat** folder at the root of the Home folder\. Leave the JRE at its default value and choose **Finish**\.

   A **Servers** project is created in the workspace, and a Tomcat v9\.0 server is now available in the **Servers** view\. This is where the compiled application will be deployed and started:  
![\[Blu Age Eclipse Servers tab with new Tomcat server listed.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/ba-eclipse-server-added.png)

## Step 7: Deploy to Tomcat<a name="tutorial-ba-developer-deploy-tomcat"></a>

In this step, you deploy the Planets demo application to the Tomcat server so you can run it\.

1. Right click `PlanetsDemo-web` and choose **Run As > Maven install**\. Then right click `PlanetsDemo-web` again and choose **Refresh** to ensure that the npm\-compiled frontend is properly compiled to a \.war and noticed by Eclipse\.

1. Upload the [PlanetsDemo\-runtime\.zip](https://d3lkpej5ajcpac.cloudfront.net/appstream/bluage/developer-ide/PlanetsDemo/PlanetsDemo-runtime.zip) to the instance, and unzip it at an accessible location\. This provides some configuration folders and files needed for the demo application to run\.

1. Copy the contents of `PlanetsDemo-runtime/tomcat-config` into the `Servers/Tomcat v9.0...` subfolder created for your Tomcat server:  
![\[Blu Age Eclipse Tomcat v9.0 subfolder and the files it contains.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/ba-eclipse-tomcat-subfolder.png)

1. Double\-click the `tomcat v9.0` server entry in the Servers view\. The server properties editor appears:  
![\[The server properties editor. The Overview tab is selected.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/ba-eclipse-server-properties-editor.png)

1. In the **Overview** tab, increase the **Timeouts** values to 450 seconds for Start and 150 seconds for Stop, as shown:  
![\[Start timeout value set to 450 seconds. Stop timeout value set to 150 seconds.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/ba-eclipse-timeout-values.png)

1. Choose **Open launch configuration**\. In the resulting wizard, navigate to the **Arguments** folder and, for **Working directory**, choose **Other**, **File System**, and navigate to the `PlanetsDemo-runtime` folder unzipped earlier \(one of its direct subfolders should be **config**\):  
![\[The Edit Configurations dialog box with the working directory specified in the Other field.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/ba-eclipse-edit-configuration.png)

1. Choose the **Modules** tab of the server properties editor and make the following changes:
   + Choose **Add Web Module** and add `PlanetsDemo-service`\.
   + Choose **Add External Web Module** and make the following changes:
     + for **Document base**, enter the path to `~/webapps/gapwalk-application...war`
     + for **Path**, enter `/gapwalk-application`
     + choose OK\.
   + Choose **Add External Web Module** again and make the following changes:
     + for **Document base**, enter the path to the frontend \.war \(in `PlanetsDemo-web/target`\)
     + for **Path**, enter `/demo`
     + choose OK
   + Save the editor modifications \(Ctrl \+ S\)\.

The editor content should now be similar to the following:

![\[The Tomcat server properties Web Modules tab with the modules listed.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/ba-eclipse-web-modules.png)

## Step 8: Create the JICS database<a name="tutorial-ba-developer-create-jics"></a>

In this step, you connect to the database you created in [Step 1: Create a database](#tutorial-ba-developer-create-db)\.

1. From the AppStream 2\.0 instance, launch `pgAdmin` by issuing the following command in a terminal:

   ```
         ./pgadmin-start.sh
   ```

1. In that same terminal, choose a login \(email address\) and password, then take note of the provided url \(typically http://127\.0\.0\.1:5050 \)\. Launch Google Chrome in the instance, then copy and paste this URL there and login using the identifiers you chose beforehand:  
![\[The pgAdmin login dialog box.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/ba-pgadmin-login.png)

1. When you are logged in, choose **Add New Server** and enter the connection information to the previously created database:  
![\[The pgAdmin Register Server dialog box. The Connection tab is selected.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/ba-pgadmin-register-server.png)

1. When you are connected to the database server, use **Object > Create > Database** and create a new database named **jics**\.

   Finally, edit database connection information used by the demo app\. Those are defined in `PlanetsDemo-runtime/config/application-main.yml`\. Search for the `jicsDs` entry, and edit its properties to match your database information\.

## Step 9: Start and test the application<a name="tutorial-ba-developer-test-app"></a>

In this step, you start the Tomcat server and the demo application so you can test it\.

1. To start the Tomcat server and the previously deployed applications, right click its entry in the Servers view and choose **Start**\. A console will appear in which the startup logs are displayed\.

1. After the server starts \(you can check its status in the **Servers** view, or wait for the **Server startup in \[xxx\] milliseconds** message in the console\), check that gapwalk\-application is properly deployed by accessing the **http://localhost:8080/gapwalk\-application** URL in a Google Chrome browser\. You should get the following answer:  
![\[Confirmation message showing that the jics application is running.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/ba-jics-run-confirm.png)

1. Then access the deployed application frontend from Google Chrome using the following URL: http://localhost:8080/demo\. The following **Transaction Launcher** page should be displayed:  
![\[The JICS transaction launcher page.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/ba-jics-launcher.png)

1. Enter `PINQ` in the input field and choose **Run** \(or press Enter\) to start the application transaction\.

   The demo app screen should then be displayed:  
![\[The PlanetsDemo application screen in insert mode.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/ba-demo-app-screen.png)

1. Type a planet name in the corresponding field and press Enter:  
![\[The PlanetsDemo application screen with Earth entered in the Planet name field.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/ba-demo-with-data.png)

## Step 10: Debug the application<a name="tutorial-ba-developer-debug"></a>

In this step, you test using the standard Eclipse debugging features, which are available when working on a modernized application\.

1. Open the main service class by pressing Ctrl \+ Shift \+ T and entering `PlanetsinqProcessImpl`:  
![\[The Eclipse Open Type dialog box with PlanetsinqProcessImpl entered.\]](http://docs.aws.amazon.com/m2/latest/userguide/images/ba-eclipse-open-type.png)

1. Navigate to the `searchPlanet` method, and put a breakpoint there\.

1. Restart the server: Right click > Restart in Debug\.

1. Repeat the previous scenario \(access the application, input a planet name and press Enter\)\.

   Eclipse will halt in the `searchPlanet` method; now you can examine it\.

## Clean up resources<a name="tutorial-ba-developer-clean"></a>

If you no longer need the resources you created for this tutorial, delete them so that you won't continue to be charged for them\. Complete the following steps:
+ If the Planets application is still running, stop it\.
+ Delete the database you created in [Step 1: Create a database](#tutorial-ba-developer-create-db)\. For more information, see [Deleting a DB instance](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_DeleteInstance.html)\.