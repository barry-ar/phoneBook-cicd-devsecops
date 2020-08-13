# Complete integration chain devsecops

![infrastructure](https://user-images.githubusercontent.com/50138085/90188810-62d5f280-ddbc-11ea-97ca-88006cfcd2c5.PNG)


## Tools
 **Cloud** : Amazone Web Service (EC2, CloudFormation)
 
 **Container**: Docker 
 **Automated deployments**: Ansible 
 **Continuous integration** : Jenkins
 
 **binary repository manager**: Jfrog artifactory
 
 **Source Code Managment** : Github
 
 **tests of security**: Jmeter
 
 **Security**: Clair, Gauntlt
 
 **Notification**: Slack

## Context

The goal of this project is to deploy an application consisting of a database (backend) and the frontend by setting up a complete integration chain.

Description
I tried to reproduce the different stages of the implementation of a CI / CD chain.
Deployment of four servers on AWS (build, preprod, prod).
1 -the main ** server which contains the main devops tools (Jenkins (link to install jenkins via ansible), Ansible, Docker, Jmeter)
2 -the **build** server only allows to build images
3 - Server **pre-production** allows to deploy the application in order to allow the developer to test the application
4 - the **production** server to deploy the application, accessible by the users

* **AWS**: The servers are deployed in the cloud using Amazone Web Service
* **docker** for containerized the application
* **Ansible** to configure the infrastructure and automatically deploy the applications.
* **Jenkins** to orchestrate the different stages of the deployment
* **Jfrog artifactory**: is a binary repository manager *(Private Registry)*, to store artifacts

Job
Two environment, pre-production (the branch dev) and the production environment (the branch master)
The branch **dev** to allow the developer to test the proper functioning of the application
The branch **master** for the deployment of the application

The principle of operation is as follows:

* Code modification and **push** on docker hub;
  Thanks to the webhooks, triggering of the first pipeline which will make it possible to check the syntaxes (yml, docker-compose, python ...), these are unit tests.
  Then notification on slack of the result of the pipeline.
* If the pipeline result is **success**, automatic triggering of the second pipeline.
  Ansible syntax checks, environment configuration, then build images on the build server.
* **Security**: vulnerability scan of images, then **push** on the private registry **jfrog artifactory**
  follow this link for the implementation of an artifactory server.
  Deployment of the application on the **pre-production** server to test the functionality of the application.
* **Security test**: Generation of different types of attacks.

After validation of the previous steps merge request in order to pass the modifications on the **master** branch
The application is deployed automatically on the **production** server and a **slack** notification is sent.

## Technical word

```
Jenkins, Pipeline, Shared-library,  Ansible, Playbooks, Rôles, Galaxy, Docker, 
Jfrog artifactory , slack, Clair, Gauntlt, Jmeter,  Push, Merge Request
```
