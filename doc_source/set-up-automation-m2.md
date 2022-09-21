# Set up Automation for Micro Focus Enterprise Analyzer and Micro Focus Enterprise Developer Streaming Sessions<a name="set-up-automation-m2"></a>

You can automatically run a script at session start and end to allow automation that is specific to your customer context\. For more information on this AppStream 2\.0 feature, see [Use Session Scripts to Manage Your AppStream 2\.0 Users' Streaming Experience](https://docs.aws.amazon.com/appstream2/latest/developerguide/use-session-scripts.html) in the *Amazon AppStream 2\.0 Administration Guide*\.

This feature requires that you have at least the following versions of the Enterprise Analyzer and Enterprise Developer images:
+ `m2-enterprise-analyzer-v7.0.4.R1`
+ `m2-enterprise-developer-v7.0.7.R1`

**Topics**
+ [Set up automation at session start](#set-up-automation-m2.start)
+ [Set up automation at session end](#set-up-automation-m2.end)

## Set up automation at session start<a name="set-up-automation-m2.start"></a>

If you want to run an automation script when users connect to AppStream 2\.0, create your script and name it `m2-user-setup.cmd`\. Store the script in the AppStream 2\.0 Home folder for the user\. The AppStream 2\.0 images that AWS Mainframe Modernization provides look for a script with that name in that location, and run it if it exists\.

**Note**  
The script duration cannot exceed the limit set by AppStream 2\.0, which is currently 60 seconds\. For more information, see [Run Scripts Before Streaming Sessions Begin](https://docs.aws.amazon.com/appstream2/latest/developerguide/use-session-scripts.html#run-scripts-before-streaming-sessions-begin) in the *Amazon AppStream 2\.0 Administration Guide*\.

## Set up automation at session end<a name="set-up-automation-m2.end"></a>

If you want to run an automation script when users disconnect from AppStream 2\.0, create your script and name it `m2-user-teardown.cmd`\. Store the script in the AppStream 2\.0 Home folder for the user\. The AppStream 2\.0 images that AWS Mainframe Modernization provides look for a script with that name in that location, and run it if it exists\.

**Note**  
The script duration cannot exceed the limit set by AppStream 2\.0, which is currently 60 seconds\. For more information, see [Run Scripts After Streaming Sessions End](https://docs.aws.amazon.com/appstream2/latest/developerguide/use-session-scripts.html#run-scripts-after-streaming-sessions-end) in the *Amazon AppStream 2\.0 Administration Guide*\.