# AWS Mainframe Modernization data set definition reference<a name="datasets-m2-definition"></a>

If your application requires more than a few data sets for processing, entering them one by one in the AWS Mainframe Modernization console is inefficient\. Instead, you can create a JSON file to specify each dataset\. The following example shows the JSON for specifying the required data sets for the BankDemo sample\.

**Topics**
+ [Dataset definition JSON file](#applications-m2-dataset-import-define)

## Dataset definition JSON file<a name="applications-m2-dataset-import-define"></a>

```
{
    "dataSets": [{
            "dataSet": {
                "storageType": "Database",
                "datasetName": "MFI01V.MFIDEMO.BNKACC",
                "relativePath": "DATA",
                "datasetOrg": {
                    "vsam": {
                        "format": "KS",
                        "encoding": "A",
                        "primaryKey": {
                            "length": 9,
                            "offset": 5
                        }
                    }
                },
                "recordLength": {
                    "min": 200,
                    "max": 200
                }
            },
            "externalLocation": {
                "s3Location": "$S3_DATASET_PREFIX/data/MFI01V.MFIDEMO.BNKACC.DAT"
            }
        },
        {
            "dataSet": {
                "storageType": "Database",
                "datasetName": "MFI01V.MFIDEMO.BNKATYPE",
                "relativePath": "DATA",
                "datasetOrg": {
                    "vsam": {
                        "format": "KS",
                        "encoding": "A",
                        "primaryKey": {
                            "length": 1,
                            "offset": 0
                        }
                    }
                },
                "recordLength": {
                    "min": 100,
                    "max": 100
                }
            },
            "externalLocation": {
                "s3Location": "$S3_DATASET_PREFIX/data/MFI01V.MFIDEMO.BNKATYPE.DAT"
            }
        },
        {
            "dataSet": {
                "storageType": "Database",
                "datasetName": "MFI01V.MFIDEMO.BNKCUST",
                "relativePath": "DATA",
                "datasetOrg": {
                    "vsam": {
                        "format": "KS",
                        "encoding": "A",
                        "primaryKey": {
                            "length": 5,
                            "offset": 0
                        }
                    }
                },
                "recordLength": {
                    "min": 250,
                    "max": 250
                }
            },
            "externalLocation": {
                "s3Location": "$S3_DATASET_PREFIX/data/MFI01V.MFIDEMO.BNKCUST.DAT"
            }
        },
        {
            "dataSet": {
                "storageType": "Database",
                "datasetName": "MFI01V.MFIDEMO.BNKHELP",
                "relativePath": "DATA",
                "datasetOrg": {
                    "vsam": {
                        "format": "KS",
                        "encoding": "A",
                        "primaryKey": {
                            "length": 8,
                            "offset": 0
                        }
                    }
                },
                "recordLength": {
                    "min": 83,
                    "max": 83
                }
            },
            "externalLocation": {
                "s3Location": "$S3_DATASET_PREFIX/data/MFI01V.MFIDEMO.BNKHELP.DAT"
            }
        },
        {
            "dataSet": {
                "storageType": "Database",
                "datasetName": "MFI01V.MFIDEMO.BNKTXN",
                "relativePath": "DATA",
                "datasetOrg": {
                    "vsam": {
                        "format": "KS",
                        "encoding": "A",
                        "primaryKey": {
                            "length": 16,
                            "offset": 26
                        }
                    }
                },
                "recordLength": {
                    "min": 400,
                    "max": 400
                }
            },
            "externalLocation": {
                "s3Location": "$S3_DATASET_PREFIX/data/MFI01V.MFIDEMO.BNKTXN.DAT"
            }
        }

    ]
}
```

Each data set definition has the same structure\. All of the definitions are grouped into an array, `dataSets`, as shown in the example\. Use the following structure to specify the definition of each data set:

```
            "dataSet": {
                "storageType": "Database",
                "datasetName": "MFI01V.MFIDEMO.BNKACC",
                "relativePath": "DATA",
                "datasetOrg": {
                    "vsam": {
                        "format": "KS",
                        "encoding": "A",
                        "primaryKey": {
                            "length": 9,
                            "offset": 5
                        }
                    }
                },
                "recordLength": {
                    "min": 200,
                    "max": 200
                }
            },
            "externalLocation": {
                "s3Location": "$S3_DATASET_PREFIX/data/MFI01V.MFIDEMO.BNKACC.DAT"
            }
```

**storageType**  
Optional\. Specifies whether imported data sets are stored in a database or a file system\.  
+ If you are using the Micro Focus runtime engine, you can use either `database` to indicate a datastore or `file system` to indicate either Amazon EFS or Amazon FSx\.
+ If you are using the Blu Age runtime engine, `file system` is not supported\. You must use `database` to indicate Blusam\.

**datasetName**  
Required\. The logical identifier for a specific data set, expressed in mainframe format\.

**relativePath**  
Optional\. The relative location of the data set in the database or file system\.

**datasetOrg**  
Required\. The type of data set\. Possible values are `gdg` or `vsam`\. Each type of data set requires additional information, as follows:  
+ `gdg` \- generation data group \(GDG\)\. This information is optional\.
  + `Limit` \- the maximum number of generation data sets, up to 255, in a generation data group \(GDG\)\.
  + `rollDisposition` \- the disposition of the data set in the catalog\.
+ `vsam` \- virtual storage access method \(VSAM\)\. This information is optional\.
  + `format` \- The record format of the data set\. Possible values are 
  + `encoding` \- The character set used by the data set\. Possible values are ASCII, EBCDIC, or unknown\.
  +  `primaryKey` \- The primary key of a KSDS data set\. The `length` and `offset` values are required\. 

**recordLength**  
The length of a record\. This information is required\. You must specify both the minimum and maximum length of the record\.

All of the BankDemo sample data sets are VSAM data sets\.