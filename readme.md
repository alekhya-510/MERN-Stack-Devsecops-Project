**Enterprise DevSecOps: MERN Stack on AWS EKS with Terraform, Jenkins, & Security Scanning**

In this project we will deploy the MERN stack in kubernetes cluster through Argo CD deployment and monitoring through prometheus and grafana

The project is mainly divided into 3 stages 
1. Infrstructure provision through terraform
2. Continous Integration and Deploying the application through ArgoCD
3. Monitoring the application

**a. Automating Infrstructure provision through terraform and jenkins pipeline :**

In this step we need to provision the AWS EKS cluster through terraform and terraform automated by writing the jenkins pipeline.For that will create a Virtual Server as follows.

Creating Jenkins server:

EC2 instance specification:
Application and OS Image : Ubuntu 22.04
Instance type: t3.xlarge
Keypair : Proceed witout keypair
Stotrage : 30gb










Opening ports for jenkins and sonarqube : 8080 & 9000






configuring storage to 30gb and adding instance profile 




updating user data : automating installation of java , jenkins , docker, sonarqube , aws-cli, kubectl , eksctl , terraform , trivy , helm

Once the instance is up and running we need to connect with SSM Sessions Manager and verify that everthing is installed , we need to configure the jenkins.

**Jenkins login : <ip-aadress>:8080**



We need to install the required plugins for pipeline



Once they have installed we need to configure the tools as well


**Terraform** :








**Sonarqube** : 





Now we will run the pipeline , with the help of aws credentials will provision the EKS cluster


<img width="906" height="675" alt="Screenshot 2026-02-27 at 9 10 45 pm" src="https://github.com/user-attachments/assets/c3f8fa2a-8154-433f-a875-0c55b6ca87c9" />


We can see that cluster is provisioned and ready.

**EKS-Cluster**



Cluster is created in private vpc and can be accessed within vpc only,to access the clster we need to create jump server.

<img width="1797" height="180" alt="Screenshot 2026-02-27 at 9 13 26 pm" src="https://github.com/user-attachments/assets/36a36b73-352d-4dcb-bb7c-88fa099da9d4" />


**Creating Jump Server:**

We have created the jump server in the same vpc of EKS cluster to be in public subnet with the following specifications:

Instance name : Jump-Server
Instance type : t2.medium
OS : Ubuntu

In the security group we have chosen the newly created vpc and public subent is choosen.
In the jump server we need to install AWS CLI , eksctl , kubectl ,helm .That installations mentioned in userdata.


<img width="2295" height="332" alt="Screenshot 2026-02-27 at 9 14 24 pm" src="https://github.com/user-attachments/assets/21b8fba9-c617-418b-9e23-c2e2908b5a37" />



