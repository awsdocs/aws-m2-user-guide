# Concepts<a name="concept-m2"></a>

AWS Mainframe Modernization provides tools and resources to help you migrate, modernize, and run mainframe workloads on AWS\. 

**Topics**
+ [Application](#application-concept)
+ [Application definition](#appdef-concept)
+ [Batch job](#batch-job-concept)
+ [Configuration](#configuration-concept)
+ [Data set](#data-set-concept)
+ [Environment](#environment-concept)
+ [Mainframe modernization](#modernization-concept)
+ [Migration journey](#migration-concept)
+ [Mount point](#mount-concept)
+ [Automated Refactoring](#refactor-concept)
+ [Replatforming](#replatform-concept)
+ [Resource](#resource-concept)
+ [Runtime engine](#runtime-concept)

## Application<a name="application-concept"></a>

A running mainframe workload in AWS Mainframe Modernization\. A set of batch jobs, interactive transactions \(CICS or IMS\), or other components comprise an application\. You define the scope\. You must define and specify any components or resources that the workload needs, such as CICS transactions or batch jobs\.

## Application definition<a name="appdef-concept"></a>

The definition or specification of the components and resources needed by an application \(mainframe workload\) running in AWS Mainframe Modernization\. Separating the definition from the application itself is important because it is possible to reuse the same definition for multiple stages \(Pre\-production, Production\), represented by different runtime environments\. 

## Batch job<a name="batch-job-concept"></a>

A scheduled program that is configured to run without requiring user interaction\. In AWS Mainframe Modernization, you will need to store both batch job JCL files and batch job binaries in an Amazon S3 bucket, and provide the location of both in the application definition file\. When you run a batch job, AWS Mainframe Modernization reports the following status values:

**Submitting**  
The batch job is in the process of being submitted\.

**Holding**  
The batch job is on hold\.

**Dispatching**  
The batch job is in the process of being dispatched\.

**Running**  
The batch job is currently running\.

**Cancelling**  
The batch job is in the process of being cancelled\.

**Cancelled**  
The batch job is cancelled\.

**Succeeded**  
The batch job finished running successfully\.

**Failed**  
The batch job failed\.

**Succeeded With Warning**  
The batch job finished running successfully with a minor error reported\. The job condition code returned as part of the GetBatchJobExecution response indicates the cause of the error\.

## Configuration<a name="configuration-concept"></a>

The characteristics of an environment or application\. Environment configurations consist of engine type, engine version, availability patterns, optional file system configurations, and more\. 

Application configurations can be static or dynamic\. Static configurations change only when you update an application by deploying a new version\. Dynamic configurations, which are usually an operational activity such as turning tracing on or off, change as soon as you update them\.

## Data set<a name="data-set-concept"></a>

A file containing data for use by applications\. 

## Environment<a name="environment-concept"></a>

A named combination of AWS compute resources, a runtime engine, and configuration details created to host one or more applications\.

## Mainframe modernization<a name="modernization-concept"></a>

The process of migrating applications from a legacy mainframe environment to AWS\.

## Migration journey<a name="migration-concept"></a>

The end\-to\-end process of migrating and modernizing legacy applications, typically made of the following phases: Assess, Mobilize, Migrate and modernize, and Operate and optimize\.

## Mount point<a name="mount-concept"></a>

A directory in a file system that provides access to the files stored within that system\.

## Automated Refactoring<a name="refactor-concept"></a>

The process of modernizing legacy application artifacts for running in a modern cloud environment\. It can include code and data conversion\. For more information, see [AWS Mainframe Modernization Automated Refactor](https://aws.amazon.com/mainframe-modernization/patterns/refactor/?mainframe-blogs.sort-by=item.additionalFields.createdDate&mainframe-blogs.sort-order=desc)\.

## Replatforming<a name="replatform-concept"></a>

The process of moving an application and application artifacts from one computing platform to a different computing platform\. For more information, see [AWS Mainframe Modernization Replatform](https://aws.amazon.com/mainframe-modernization/patterns/replatform/)

## Resource<a name="resource-concept"></a>

A physical or virtual component within a computer system\.

## Runtime engine<a name="runtime-concept"></a>

Software that facilitates the running of an application\.