# Error: Time out while waiting for dataset name to be unlocked<a name="ba-blusam-timeout"></a>
+ Engine: Blu Age
+ Component: Blusam

If you see this error in the Amazon CloudWatch logs for a AWS Mainframe Modernization application using the Blu Age engine and running in an environment with the High Availability pattern, it indicates that another application is holding a lock on a shared dataset\. Typically, this situation occurs if the other application crashes or otherwise fails and does not release the lock\.

## How this error occurs<a name="ba-blusam-timeout-know"></a>

Application `example-app-1` tries to lock a record `example-record-1` for a write operation\. This operation creates both a lock on dataset `example-dataset-1`, which owns `example-record-1`, and a lock on `example-record-1` itself\. Now another application, `example-app-2`, tries to lock the same record `example-record-1`\. The dataset and the record are already locked, so `example-app-2` waits for the lock to release\. If `example-app-1` crashes, the held lock on dataset `example-dataset-1` still exists, which causes `example-app-2` to cancel its write attempt and raise a timeout exception\. This deadlock situation prevents all applications from reaching `example-dataset-1`\.

## How do you know if this is your situation?<a name="ba-blusam-timeout-situation"></a>

Look for a failed application and check whether it uses the same dataset mentioned in the error message\. Check whether the application is running in a runtime environment with the High Availability pattern\. The application that raised the timeout exception cannot proceed and will display the `Failed` status\.

## What can you do?<a name="ba-blusam-timeout-actions"></a>

To resolve the situation immediately, you can force the lock to release\. To prevent a similar situation from occurring in the future, you can configure two parameters that control the Blusam auto repairing mechanism\.

## Force the lock to release<a name="ba-blusam-timeout-force"></a>

The Blusam lock manager uses Amazon ElastiCache for Redis to provide shared locks between applications\. To release locks in ElastiCache, use the Redis CLI utility\. You cannot delete an individual record lock\. You must remove all locks from the owning dataset\. Complete the following steps:

1. Connect to your ElastiCache using the following command:

   ```
   redis-cli -h hostname -p port
   ```

   You can find the details of your ElastiCache in the ElastiCache console at [ https://console\.aws\.amazon\.com/elasticache/](https://console.aws.amazon.com/elasticache/)\.

1. Enter your password\.

1. Enter the command you want to run, as follows:    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/m2/latest/userguide/ba-blusam-timeout.html)

## Configure the Blusam auto repairing mechanism<a name="ba-blusam-timeout-auto-repair"></a>

The Blusam locks manager includes an auto repairing mechanism to prevent deadlocks on datasets or records\. You can adjust the following parameters in the application definition \(`application-main.yml`\) to configure the auto repairing mechanism:
+ `locksDeadTime`: refers to the maximum time an application can hold a lock\. When this time passes, the lock is declared expired and released immediately\. The `locksDeadTime` value is in milliseconds, and the default value is 1000\.
+ `locksCheck`: defines the Blusam locks manager strategy for checking locks\. All Blusam locks in ElastiCache are timestamped and have an expiration time\. The `locksCheck` parameter value determines whether expired locks are removed\.
  + `off`: no check is executed at any time\. Deadlocks might occur\. \(Not recommended\)
  + `reboot`: checks are executed when an AWS Mainframe Modernization application instance running in an AWS Mainframe Modernization runtime environment is started or rebooted\. All expired locks are released immediately\. \(Default\)
  + `timeout`: checks are executed when an AWS Mainframe Modernization application instance running in an AWS Mainframe Modernization runtime environment is started or rebooted, or when a timeout expires during an attempt to lock a dataset\. Expired locks are released immediately\.

For more information on the application definition for a Blu Age application, see [Blu Age application definition sample](applications-m2-definition.md#applications-m2-definition-ba)\.

## Blusam locks manager<a name="ba-blusam-timeout-locks-mgr"></a>

In the context of an AWS Mainframe Modernization runtime environment using the High Availability pattern, a Blu Age application might be deployed multiple times\. For those applications that handle Blusam data sets, concurrent access problems might occur\. The Blusam locks manager ensures data integrity and manages read and write access to records and data sets by providing shared locks between applications using ElastiCache\. This mechanism allows more than one application to read the record concurrently, and ensures that only one application at a time writes the record\.

### Write locks<a name="ba-blusam-timeout-locks-mgr-write"></a>

To update or delete a specific record, the application must first lock the dataset that owns the record, then lock the record itself\. When the record is locked, the dataset lock is released, and other records from the same dataset are available for use\. When the update or delete operation is complete, the held record lock is released\. Only one application at a time can update the record, which blocks other applications from either reading or writing until the lock is released, if the defined application policy allows waiting for release\.

### Read locks<a name="ba-blusam-timeout-locks-mgr-read"></a>

As long as no write lock is held on the record or the dataset, multiple applications can read the same records at the same time\. To lock a record for a write operation, all read locks must be released\.

**Note**  
The Blusam locks manager handles the access from multiple threads in a given application using the same locking mechanism\.