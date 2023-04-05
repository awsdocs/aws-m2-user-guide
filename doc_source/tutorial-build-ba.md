# Tutorial: Setting up the build for the Planets demo app<a name="tutorial-build-ba"></a>

This guide explains how to set up a build and deployment pipeline for the Planets demo application on an Amazon Elastic Compute Cloud instance\. It uses AWS CodeArtifact, AWS CodeBuild, AWS CodeCommit, and AWS CodePipeline to store the project dependencies and source code and set up a build project and a pipeline\. When you finish this tutorial, you'll have a working pipeline that can trigger an automatic build and deploy whenever you make changes to the source code\.

**Note**  
This tutorial requires you to create resources in other AWS services\. These resources might result in charges to your AWS account\.

**Topics**
+ [Prerequisites](#tutorial-build-ba-prerequisites)
+ [Overview of tutorial](#tutorial-build-ba-overview)
+ [Set up your CI/CD environment](tutorial-build-ba-env.md)
+ [Set up your CI/CD build and pipeline](tutorial-build-ba-cicd.md)
+ [Clean up resources](tutorial-build-ba-clean.md)

## Prerequisites<a name="tutorial-build-ba-prerequisites"></a>

Before you begin, make sure you can access both the AWS Management Console, and an Amazon Elastic Compute Cloud instance that you’ll use to set up the build and the pipeline\. If you don’t have an Amazon EC2 instance available, create one and connect to it\. Then download, install, and configure the following software\. For best results, we recommend that you install and configure each application as if you are doing it for the first time\.
+ Download [PlanetsDemo\-pom\.zip](https://d3lkpej5ajcpac.cloudfront.net/appstream/bluage/developer-ide/PlanetsDemo/PlanetsDemo-pom.zip) and unzip it to a folder of your choice\. This file contains the PlanetsDemo application binaries\.
+ Download the [gapwalk](https://d3lkpej5ajcpac.cloudfront.net/library/bluage/gapwalk-3.1.0-b3257-SNAPSHOT-stub.zip) distribution and unzip it to a folder of your choice\. This file contains the framework binaries\.
+ Download [build\-pipeline\.zip](https://drm0z31ua8gi7.cloudfront.net/cicd/bluage/build-pipeline.zip) \(US\) or [build\-pipeline\.zip](https://d3lkpej5ajcpac.cloudfront.net/cicd/bluage/build-pipeline.zip) \(Europe\) and unzip it to a folder of your choice\. This archive contains configuration files and scripts\.
+ Download and install [Maven](https://maven.apache.org/)\. Maven is a build automation tool\.
+ If necessary, download and install a [Java Development Kit \(JDK\)](https://www.oracle.com/java/technologies/downloads/) \(version 8 or 11\)\. The JDK is a collection of software tools and libraries for Java applications\.
+ Download and install [Git](http://git-scm.com/)\. Git is a distributed version control system\.

## Overview of tutorial<a name="tutorial-build-ba-overview"></a>

This tutorial contains two sections:
+ Set up CI/CD environment tasks\. Most of these are one\-time only tasks\. Many of them require you to create resources in other AWS services\. This tutorial includes all the steps to create these resources\.
+ Create build and pipeline tasks\. These are tasks that you typically complete at least once for every application or code project\.

**Note**  
If you already have a CodeArtifact repo with all the dependencies, you can skip the Maven and the CodeArtifact steps\.