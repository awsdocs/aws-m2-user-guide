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

A running mainframe workload in AWS Mainframe Modernization\. An application might run a batch job, a CICS transaction, or something else\. You define the scope\. Any components or resources that the workload needs, such as CICS transactions, batch jobs, a listener configuration: you must define and specify these\.

## Application definition<a name="appdef-concept"></a>

The definition or specification of the components and resources needed by an application \(mainframe workload\) running in AWS Mainframe Modernization\. Separating the definition from the application itself is important because it is possible to reuse the same definition for multiple applications or stages \(Pre\-production, Production\)\. Each application can have multiple definitions\. Because an application definition is a resource, you can restrict access to it\. This provides a flexible permission model\.

## Batch job<a name="batch-job-concept"></a>

A scheduled program that is configured to run without requiring user interaction\. In AWS Mainframe Modernization, you will need to store both batch job JCL files and batch job binaries in an Amazon S3 bucket, and provide the location of both in the application definition file\. 

## Configuration<a name="configuration-concept"></a>

The characteristics of an environment or application\. Environment configurations consist of Amazon FSx or Amazon EFS file system configurations, if used\. 

Application configurations can be static or dynamic\. Static configurations change only when you update an application by deploying a new version\. Dynamic configurations, which are usually an operational activity such as turning tracking on or off, change as soon as you update them\.

## Data set<a name="data-set-concept"></a>

A file containing data for use by applications\. 

## Environment<a name="environment-concept"></a>

A named combination of AWS compute resources, a runtime engine, and configuration details created to host one or more applications\.

## Mainframe modernization<a name="modernization-concept"></a>

The process of migrating applications from a legacy mainframe environment to AWS\.

## Migration journey<a name="migration-concept"></a>

The end\-to\-end process of migrating and modernizing legacy applications, typically made of the following four steps: Analyze, Develop, Deploy, and Operate\.

## Mount point<a name="mount-concept"></a>

A directory in a file system that provides access to the files stored within that system\.

## Automated Refactoring<a name="refactor-concept"></a>

The process of modernizing legacy application artifacts for running in a modern cloud environment\. It can include code and data conversion\. 

## Replatforming<a name="replatform-concept"></a>

The process of moving an application and application artifacts from one computing platform to a different computing platform\.

## Resource<a name="resource-concept"></a>

A physical or virtual component within a computer system\.

## Runtime engine<a name="runtime-concept"></a>

Software that facilitates the running of an application\.