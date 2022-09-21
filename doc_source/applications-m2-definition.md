# AWS Mainframe Modernization application definition reference<a name="applications-m2-definition"></a>

In AWS Mainframe Modernization, you configure migrated mainframe applications in an application definition JSON file, which is specific to the runtime engine you choose\. An application definition contains both general information and engine\-specific information\. This topic describes both the Blu Age and Micro Focus application definitions and identifies all required and optional elements\.

**Topics**
+ [General header section](#applications-m2-definition-general)
+ [Definition section overview](#applications-m2-definition-overview)
+ [Blu Age application definition sample](#applications-m2-definition-ba)
+ [Blu Age definition details](#applications-m2-definition-ba-details)
+ [Micro Focus application definition](#applications-m2-definition-mf)
+ [Micro Focus definition details](#applications-m2-definition-mf-details)

## General header section<a name="applications-m2-definition-general"></a>

Each application definition starts with general information about the template version and source locations\. The current version of the application definition is 2\.0\. Although version 1 still works, it is on a deprecation path\. We recommend that you use version 2 when you create or update applications\.

Use the following structure to specify the template version and source locations\.

```
 "template-version": "2.0",
    "source-locations": [
        {
            "source-id": "s3-source",
            "source-type": "s3",
            "properties": {
                "s3-bucket": "mainframe-deployment-bucket-aaa",
                "s3-key-prefix": "v1"
            }
        }
    ]
```

**template\-version**  
Required\. Specifies the version of the application definition file\. Do not change this value\. Currently, the only allowed value is 2\.0\. Specify `template-version` with a string\.

**source\-locations**  
Specifies the locations of the files and other resources that the application requires during runtime\.

**properties**  
Provides the details of the source location\. Each property is specified with a string\.  
+ `s3-bucket` \- Required\. Specifies the name of the Amazon S3 bucket where the files are stored\.
+ `s3-key-prefix` \- Required\. Specifies the name of the folder in the Amazon S3 bucket where the files are stored\.

## Definition section overview<a name="applications-m2-definition-overview"></a>

Specifies the resource definitions of the services, settings, data, and other typical resources that the application needs to run\. When you update an application definition, AWS Mainframe Modernization detects changes by comparing the `source-locations` and `definition` lists from both the previous and the current versions of the application definition JSON file\.

The definition section is engine\-specific and subject to change\. The following sections show sample engine\-specific application definitions for both engines\.

## Blu Age application definition sample<a name="applications-m2-definition-ba"></a>

```
{
 "template-version": "2.0",
 "source-locations": [
        {
            "source-id": "s3-source",
            "source-type": "s3",
            "properties": {
                "s3-bucket": "mainframe-deployment-bucket-aaa",
                "s3-key-prefix": "v1"
            }
        }
    ],
    "definition" : {
        "listeners": [{
            "port": 8194,
            "type": "http"
        }],
        "ba-application": {
            "app-location": "${s3-source}/murachs-v6/"
        },
        "blusam": {
            "db": {
                "nb-threads": 8,
                "batch-size": 10000,
                "name": "blusam",
                "secret-manager-arn": "arn:aws:secretsmanager:us-west-2:111122223333:secret:blusam-FfmXLG"
            },
            "redis": {
                "hostname": "blusam.c3geul.ng.0001.usw2.cache.amazonaws.com",
                "port": 6379,
                "useSsl": true,
                "secret-manager-arn": "arn:aws:secretsmanager:us-west-2:111122223333:secret:bluesamredis-nioefm"
            }
        }
    }
}
```

## Blu Age definition details<a name="applications-m2-definition-ba-details"></a>

### Listener\(s\) \- required<a name="applications-m2-definition-ba-details-listener"></a>

Specify the port you will use to access the application through the AWS Mainframe Modernization\-created Elastic Load Balancing\. Use the following structure:

```
        "listeners": [{
            "port": 8194,
            "type": "http"
        }],
```

**port**  
Required\. You can use any available port, although we recommend using the range from 8192 to 8199\. Make sure there’s no other listeners or applications operating on this port\.

**type**  
Required\. Currently, only `http` is supported\.

### Blu Age application \- required<a name="applications-m2-definition-ba-details-application"></a>

Specify the location where the engine picks up the application image file using the following structure\.

```
         "ba-application": {
            "app-location": "${s3-source}/murachs-v6/"
        },
```

**app\-location**  
The specific location in Amazon S3 where the application image file is stored\.

### BluSAM \- required<a name="applications-m2-definition-ba-details-blusam"></a>

Specify the BluSAM database and Redis cache using the following structure\.

```
        "blusam": {
            "db": {
                "nb-threads": 8,
                "batch-size": 10000,
                "name": "blusam",
                "secret-manager-arn": "arn:aws:secretsmanager:us-west-2:111122223333:secret:blusam-FfmXLG"
            },
            "redis": {
                "hostname": "blusam.c3geul.ng.0001.usw2.cache.amazonaws.com",
                "port": 6379,
                "useSsl": true,
                "secret-manager-arn": "arn:aws:secretsmanager:us-west-2:111122223333:secret:bluesamredis-nioefm"
            }
        }
```

**db**  
Specifies the properties of the database used with the application\. The database must be an Aurora PostgreSQL database\. You can specify the following properties:  
+ `nb-threads` \- Optional\. Specifies how many dedicated threads are used for the write\-behind mechanism that the blusam engine relies on\. The default is 8\.
+ `batch-size` \- Optional\. Specifies the threshold that the write\-behind mechanism uses to start batch storage operations\. The threshold represents the number of modified records that will start a batch storage operation to ensure that modified records are persisted\. The trigger itself is based on a combination of batch\-size and an elapsed time of one second, whichever is reached first\. The default is 10000\.
+ `name` \- Optional\. Specifies the name of the database\.
+ `secret-manager-arn` \- Specifies the Amazon Resource Name \(ARN\) of the secret that contains the database credentials\. For more information, see [Step 3: Create and configure an AWS Secrets Manager secret](tutorial-runtime.md#tutorial-runtime-mf-secret)\.

**redis**  
Specifies the properties of the Redis cache that the application uses to store temporary data that it needs in a central location to improve performance\. We recommend that you both encrypt and password\-protect the Redis cache\.  
+ `hostname` \- Specifies the location of the Redis cache\.
+ `port` \- Specifies the port, typically 6379, where the Redis cache sends and receives communication\.
+ `useSsl` \- Specifies whether the Redis cache is encrypted\. If the cache is not encrypted, set `useSsl` to false\.
+ `secret-manager-arn` \- Specifies the Amazon Resource Name \(ARN\) of the secret that contains the Redis cache password\. If the Redis cache is not password\-protected, do not specify `secret-manager-arn`\. For more information, see [Step 3: Create and configure an AWS Secrets Manager secret](tutorial-runtime.md#tutorial-runtime-mf-secret)\.

### Amazon Cognito authentication and authorization handler \- optional<a name="applications-m2-definition-ba-details-cognito"></a>

AWS Mainframe Modernization uses Amazon Cognito for authentication and authorization for migrated applications\. Specify the Amazon Cognito authentication handler using the following structure\.

```
"cognito-auth-handler": {
            "user-pool-id": "cognito-idp.Region.amazonaws.com/Region_rvYFnQIxL",
            "client-id": "58k05jb8grukjjsudm5hhn1v87",
            "identity-pool-id": "Region:64464b12-0bfb-4dea-ab35-5c22c6c245f6"
}
```

**user\-pool\-id**  
Specifies the Amazon Cognito user pool that AWS Mainframe Modernization uses to authenticate users of the migrated application\. The AWS Region for the user pool should match the AWS Region for the AWS Mainframe Modernization application\.

**client\-id**  
Specifies the migrated application that the authenticated user can access\.

**identity\-pool\-id**  
Specifies the Amazon Cognito identity pool where the authenticated user exchanges a user pool token for credentials that allow the user to access AWS Mainframe Modernization\. The AWS Region for the identity pool should match the AWS Region for the AWS Mainframe Modernization application\.

## Micro Focus application definition<a name="applications-m2-definition-mf"></a>

The following sample definition section is for the Micro Focus runtime engine, and contains both required and optional elements\.

```
{
 "template-version": "2.0",
 "source-locations": [
        {
            "source-id": "s3-source",
            "source-type": "s3",
            "properties": {
                "s3-bucket": "mainframe-deployment-bucket-aaa",
                "s3-key-prefix": "v1"
            }
        }
    ],
    "definition" : {
        "listeners": [{
            "port": 5101,
            "type": "TN3270"
        }],
        "dataset-location": {
            "db-locations": [{
                "name": "Database1",
                "secret-manager-arn": "arn:aws:secrets:1234:us-east-1:secret:123456"
            }]
        },
        "cognito-auth-handler": {
            "user-pool-id": "cognito-idp.us-west-2.amazonaws.com/us-west-2_rvYFnQIxL",
            "client-id": "58k05jb8grukjjsudm5hhn1v87",
            "identity-pool-id": "us-west-2:64464b12-0bfb-4dea-ab35-5c22c6c245f6"
        },
        "batch-settings": {
            "initiators": [{
                "classes": ["A", "B"],
                "description": "initiator...."
            }],
            "jcl-file-location": "${s3-source}/batch/jcl"
        },
        "cics-settings": {
            "binary-file-location": "${s3-source}/cics/binaries",
            "csd-file-location": "${s3-source}/cics/def",
            "system-initialization-table": "BNKCICV"
        },
        "xa-resources" : [{
            "name": "XASQL",
            "secret-manager-arn": "arn:aws:secrets:1234:us-east-1:secret:123456",
            "module": "${s3-source}/xa/ESPGSQLXA64.so"
        }]
    }
}
```

## Micro Focus definition details<a name="applications-m2-definition-mf-details"></a>

The content in the definition section of the Micro Focus application definition file varies, depending on the resources that your migrated mainframe application requires at runtime\.

### Listener\(s\) \- required<a name="applications-m2-definition-mf-details-listeners"></a>

Specify a listener using the following structure:

```
"listeners": [{
    "port": 5101,
    "type": "TN3270"
}],
```

**port**  
For TN3270, the default is 5101\. For other types of service listeners, the port varies\. Each listener should have a distinctive port\. Listeners should not share ports\. For more information, see [Listener Control](https://www.microfocus.com/documentation/enterprise-developer/ed70/ES-UNIX/GUID-63F6D8B0-024F-48D1-956A-1E079E4BD891.html) in the *Micro Focus Enterprise Server* documentation\.

**type**  
Specifies the type of service listener\. For more information, see [Listeners](https://www.microfocus.com/documentation/enterprise-developer/ed70/ES-UNIX/HTPHMDSAL100.html) in the *Micro Focus Enterprise Server* documentation\.

### Dataset locations \- required<a name="applications-m2-definition-mf-details-datasets"></a>

Specify the dataset location using the following structure\.

```
"dataset-location": {
            "db-locations": [{
                "name": "Database1",
                "secret-manager-arn": "arn:aws:secrets:1234:us-east-1:secret:123456"
            }],
 }
```

**db\-locations**  
Specifies the details of the database or databases containing the imported datasets for a migrated application\. Currently, AWS Mainframe Modernization supports only datasets from a single VSAM database\.  
+ `name` \- Specifies the name of the database instance that contains the data from the dataset\.
+ `secret-manager-arn` \- Specifies the Amazon Resource Name \(ARN\) of the secret that contains the database credentials\.

### Amazon Cognito authentication and authorization handler \- optional<a name="applications-m2-definition-mf-details-cognito"></a>

AWS Mainframe Modernization uses Amazon Cognito for authentication and authorization for migrated applications\. Specify the Amazon Cognito authentication handler using the following structure\.

```
"cognito-auth-handler": {
            "user-pool-id": "cognito-idp.Region.amazonaws.com/Region_rvYFnQIxL",
            "client-id": "58k05jb8grukjjsudm5hhn1v87",
            "identity-pool-id": "Region:64464b12-0bfb-4dea-ab35-5c22c6c245f6"
}
```

**user\-pool\-id**  
Specifies the Amazon Cognito user pool that AWS Mainframe Modernization uses to authenticate users of the migrated application\. The AWS Region for the user pool should match the AWS Region for the AWS Mainframe Modernization application\.

**client\-id**  
Specifies the migrated application that the authenticated user can access\.

**identity\-pool\-id**  
Specifies the Amazon Cognito identity pool where the authenticated user exchanges a user pool token for credentials that allow the user to access AWS Mainframe Modernization\. The AWS Region for the identity pool should match the AWS Region for the AWS Mainframe Modernization application\.

### Batch settings \- required<a name="applications-m2-definition-mf-details-batch"></a>

Specify the details required by the batch jobs that run as part of the application using the following structure\.

```
 "batch-settings": {
            "initiators": [{
                "classes": ["A","B"],
                "description": "initiator...."
            }],
            "jcl-file-location": "${s3-source}/batch/jcls"
 }
```

**initiators**  
Specifies a batch initiator that starts when the migrated application starts successfully and continues running until the application stops\. You can define one or multiple classes per initiator\. You can also define multiple initiators\. For example:  

```
"batch-settings": {
            "initiators": [
                {
                  "classes": ["A", "B"],
                  "description": "initiator...."
                },
                {
                  "classes": ["C", "D"],
                  "description": "initiator...."
                }
            ],
            "jcl-file-location": "${s3-source}/batch/jcls"
 }
```
For more information, see [To define a batch initiator or printer SEP](https://www.microfocus.com/documentation/enterprise-developer/ed70/ES-UNIX/HHMTTHJCLE08.html) in the *Micro Focus Enterprise Server* documentation\.  
+ `classes` \- Specifies the job classes that the initiator can run\.
+ `description` \- Describes what the initiator is for\.
+ `jcl-file-location` \- Specifies the location of the JCL files that are required by the batch jobs the migrated application runs\.

### CICS settings \- required<a name="applications-m2-definition-mf-details-cics"></a>

Specify the details required for the CICS transactions that run as part of the application using the following structure\.

```
"cics-settings": {
            "binary-file-location": "${s3-source}/cics/binaries",
            "csd-file-location": "${s3-source}/cics/def",
            "system-initialization-table": "BNKCICV"
        }
```

**binary\-file\-location**  
Specifies the location of the CICS transaction program files\.

**csd\-file\-location**  
Specifies the location of the CICS resource definition \(CSD\) file for this application\. For more information, see [CICS Resource Definitions](https://www.microfocus.com/documentation/enterprise-developer/ed80/ES-UNIX/HRMTRHCSDS01.html) in the *Micro Focus Enterprise Sevrver* documentation\.

**system\-initialization\-table**  
Specifies the system initialization table \(SIT\) that the migrated application uses\. For more information, see [CICS Resource Definitions](https://www.microfocus.com/documentation/enterprise-developer/ed70/ES-UNIX/HRMTRHCSDS01.html) in the *Micro Focus Enterprise Server* documentation\. 

### XA resources \- required<a name="applications-m2-definition-mf-details-xa"></a>

Specify the details required for the XA resources that the application requires using the following structure\.

```
 "xa-resources" : [{
            "name": "XASQL",
            "secret-manager-arn": "arn:aws:secrets:1234:us-east-1:secret:123456",
            "module": "${s3-source}/xa/ESPGSQLXA64.so"
        }]
```

**name**  
Required\. Specifies the name of the XA resource\.

**secret\-manager\-arn**  
Specifies the Amazon Resource Name \(ARN\) for the secret that contains the credentials for connecting to the database\.

**module**  
Specifies the location of the RM switch module executable file\. For more information, see [Planning and Designing XARs](https://www.microfocus.com/documentation/enterprise-developer/ed60/ES-WIN/GUID-91C0E7E4-C012-4DF2-8996-CF6C52437FB7.html) in the *Micro Focus Enterprise Server* documentation\.