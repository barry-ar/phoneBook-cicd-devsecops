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

## The context

The goal of this project is to deploy an application consisting of a database (backend) and the frontend by setting up a complete integration chain.

The description
I tried to reproduce the different stages of the implementation
  of a CI / CD chain.
Deployment of four servers on AWS (build, preprod, prod).
1 -the **main server** which contains the main devops tools (Jenkins ([ripository link install Jenkins](https://github.com/AbdoulRahimBarry/installation-Jenkins-via-ansible)), Ansible, Docker, Jmeter)
2 -the **build** server only allows to build docker images
3 - The **pre-production** server allows the application to be deployed in order to allow the developer to test the application
4 - the **production** server to deploy the application, accessible by users

* **AWS**: Servers are deployed in the cloud using Amazon Web Service
* **docker** to containerize the application
* **Ansible** to configure the infrastructure and automatically deploy applications.
* **Jenkins** to orchestrate the various stages of deployment
* **Jfrog artifactory**: a binary repository manager *(Private Registry)*, to store artifacts, follow this link for the installation of Jfrog artifactory [ripository link install Jfrog artifactory](https://github.com/AbdoulRahimBarry/jfrog-artifactory).

### Work perform

We have two environments:
    * the pre-production environment with a **branch dev**
      which allow the developer to test the proper functioning of the application
    * the production environment with the **master branch**
       for application deployment

The principle of operation is as follows:

* After modification of the code by the developers they do a **push** on the docker hub;
  Thanks to webhooks, triggering of the first pipeline which will allow syntax checking (yml, docker-compose, python ...), then notification on **slack** on the result of the pipeline.
* If the pipeline result is **success**, automatic triggering of the second pipeline.
  Ansible syntax checks, configuration of the pre-production and production environment, then build images on the **build** server.
* **Security**: vulnerability scan of images, then **push** on the private registry **jfrog artifactory**.
  Deployment of the application on the **pre-production** server in order to test the correct functioning of the application.
* **Security test**: Generation of different types of attacks (Clear, Gauntlt and jmeter).

After validation of the previous steps, the **DSI** manager makes a merge (**Merge pull request**) in order to pass the modifications on the **master** branch
The application is automatically deployed on the **production** server, the application is available and a **slack** notification is sent.

## Technical word
```
Jenkins, Pipeline, Shared-library,  Ansible, Playbooks, Rôles, Galaxy, Docker, 
Jfrog artifactory , slack, Clair, Gauntlt, Jmeter,  Push, Merge Request
```

## References
* https://github.com/AbdoulRahimBarry/crud-application-using-flask-and-mysql-ci
* https://github.com/AbdoulRahimBarry/installation-Jenkins-via-ansible
* https://github.com/AbdoulRahimBarry/jfrog-artifactory
* https://github.com/AbdoulRahimBarry/phoneBook-ansible
* https://github.com/AbdoulRahimBarry/phoneBook-cicd-devsecops
