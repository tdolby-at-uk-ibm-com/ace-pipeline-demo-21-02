# ACE demo pipeline [![Build Status](https://travis.ibm.com/TDOLBY/ace-pipeline-demo-21-02.svg?token=xVXapEHRzgxy81sq3YrV&branch=master)](https://travis.ibm.com/TDOLBY/ace-pipeline-demo-21-02)

Demo pipeline for ACE 


![Pipeline overview](demo-slide-picture.png)

## Constituent parts

- This repo, containing the application source and tests plus the DB2 client JAR.
- Ant for building applications and running JUnit tests
- Jenkins with BlueOcean running in a container with ACE and IBM Cloud tools installed (see demo-infrastructure/docker/ace-jenkins-server)
- DB2 on a VM for component and local testing; credentials stored in Jenkins
- Docker container build files in this repo for building the application image (see scripts/preprod-container)
- IBM Cloud container registry (free tier) for hosting the application image; access credentials stored in Jenkins
- IBM Cloud Kubernetes cluster (free tier) for running the application container; access credentials stored in Jenkins
- DB2 on Cloud (free tier) for use by the application container; credentials stored in Kubernetes secrets

 Travis is also enabled on this repo to enable build and UT for PRs.

## The application

The application used to demonstrate the pipeline consists of a REST API that accepts JSON and interacts with a database, with a supporting shared library containing a lot of the code. It is designed around indexing different types of tea, storing the name and strength of the tea and assigning a unique integer id to each type so that it can be retrieved later. Audit data is logged as XML for each operation performed.

This repo can be imported into the ACE v11 toolkit using the egit plugin (version 4.11; needed to install from a downloaded p2 repo) and inspected; as most pipelines would be expected to work with source repositories, there is no project interchange file to import for the projects.

As this application exists to help demonstrate pipelines and how they work with ACE, there are some shortcuts in the code that would not normally be present in a production-ready application: the database table is created on-demand to make setup easier, the logging goes to the console instead of an audit service, etc. Ant is used for many builds to keep the steps as explicit as possible, bash is used for other builds scripts, etc.

## The tests

Unit tests reside in TeaTests and rely on TeaTestsScaffold as well as their own test data.

Component testing is run from scripts/component-test.sh and also relies on TeaTestsScaffold.

Integration testing is run from scripts/preprod-deploy-and-test.sh and tests the application end-to-end.

## How to get started

To replicate the pipeline locally, do the following:

1) Fork this repo; although cloning it locally would allow building locally, for the pipeline itself to work the source needs to be accessible to Jenkins.
2) Acquire an IBM Cloud account and create a Kubernetes cluster called "aceCluster", a Docker registry, and a DB2 on Cloud instance. More info in [cloud resources description](cloud-resources.md)
3) Build the Jenkins docker image and run it somewhere that can access the repo, link the forked repo into Jenkins Blue Ocean, and create the required credentials; see instructions in the demo-infrastructure/docker/ace-jenkins-server directory. 
4) Component testing can use either a locally-provisioned DB2 instance or a DB2 on Cloud instance; adjust the hostname, port, etc in scripts/component-test.sh appropriately, and set the JDBC credentials in Jenkins.
5) Try running the pipeline
6) Optionally, enable Travis; this requires building the Docker image in demo-infrastructure/docker/pipeline-travis-build and putting it somewhere accessible to Travis.

