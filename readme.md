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


<img width="1235" height="853" alt="Screenshot 2026-01-28 at 11 36 19 am" src="https://github.com/user-attachments/assets/1e3072e2-80d9-4990-aa23-ecb465a4c486" />







<img width="1115" height="407" alt="Screenshot 2026-01-28 at 11 36 57 am" src="https://github.com/user-attachments/assets/8759a5f6-958a-42f1-b05d-c89417d05901" />



Opening ports for jenkins and sonarqube : 8080 & 9000


<img width="1111" height="685" alt="Screenshot 2026-01-28 at 11 40 38 am" src="https://github.com/user-attachments/assets/c1c71f52-ef48-4e98-b535-6a04f825f439" />



configuring storage to 30gb and adding instance profile 


<img width="1091" height="604" alt="Screenshot 2026-01-28 at 11 43 27 am" src="https://github.com/user-attachments/assets/510a2582-ff7d-4b6a-8288-0576726ba8df" />

updating user data : automating installation of java , jenkins , docker, sonarqube , aws-cli, kubectl , eksctl , terraform , trivy , helm

Once the instance is up and running we need to connect with SSM Sessions Manager and verify that everthing is installed , we need to configure the jenkins.

**Jenkins login : <ip-aadress>:8080**


<img width="1893" height="588" alt="Screenshot 2026-02-27 at 10 54 25 am" src="https://github.com/user-attachments/assets/52ec84c1-d1fc-495c-87b3-0833de29797c" />

We need to install the required plugins for pipeline


<img width="1538" height="783" alt="Screenshot 2026-02-27 at 11 43 24 am" src="https://github.com/user-attachments/assets/6f74b621-e76e-4362-860c-b4d237110919" />

Once they have installed we need to configure the tools as well


**Terraform** :


<img width="790" height="366" alt="Screenshot 2026-02-27 at 11 58 00 am" src="https://github.com/user-attachments/assets/147f06e0-24ed-4078-8578-654fc1d2c68a" />






**Sonarqube** : 


<img width="814" height="389" alt="Screenshot 2026-02-27 at 11 51 12 am" src="https://github.com/user-attachments/assets/bed761ab-6cb7-4774-86d7-8ca4670eb8c6" />


Now we will run the pipeline , with the help of aws credentials will provision the EKS cluster



<img width="914" height="654" alt="Screenshot 2026-02-27 at 4 23 17 pm" src="https://github.com/user-attachments/assets/7ac12373-9511-4b74-8ab3-b0e9498a2d1e" />



We can see that cluster is provisioned and ready.

**EKS-Cluster**

<img width="1610" height="175" alt="Screenshot 2026-02-27 at 4 23 55 pm" src="https://github.com/user-attachments/assets/0c492697-3e1c-4fab-aea4-e3ac9a736a99" />

Cluster is created in private vpc and can be accessed within vpc only,to access the clster we need to create jump server.


<img width="790" height="390" alt="Screenshot 2026-02-27 at 11 50 20 am" src="https://github.com/user-attachments/assets/8797211e-23fc-4b03-87e2-6c12f814fc98" />

**Creating Jump Server:**

We have created the jump server in the same vpc of EKS cluster to be in public subnet with the following specifications:

Instance name : Jump-Server
Instance type : t2.medium
OS : Ubuntu

In the security group we have chosen the newly created vpc and public subent is choosen.
In the jump server we need to install AWS CLI , eksctl , kubectl ,helm .That installations mentioned in userdata.


<img width="1653" height="319" alt="Screenshot 2026-02-27 at 5 38 23 pm" src="https://github.com/user-attachments/assets/35beeca4-5ac4-446c-823f-b4cc9394aa62" />


