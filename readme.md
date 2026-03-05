**Enterprise DevSecOps: MERN Stack on AWS EKS with Terraform, Jenkins, & Security Scanning**

In this project we will deploy the MERN stack in kubernetes cluster through Argo CD deployment and monitoring through prometheus and grafana

The project is mainly divided into 3 stages 
1. Infrastructure provision through terraform
2. Continious Integration and Deploying the application through ArgoCD
3. Monitoring the application

**a. Automating Infrastructure provision through terraform and jenkins pipeline :**

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


<img width="1703" height="334" alt="Screenshot 2026-03-05 at 10 12 39 am" src="https://github.com/user-attachments/assets/a006b440-a532-4c8c-ba26-5e0042e375df" />




























**Jenkins login : <ip-aadress>:8080**

<img width="1728" height="600" alt="Screenshot 2026-03-05 at 10 16 23 am" src="https://github.com/user-attachments/assets/c748a8fa-5676-4434-9bfc-ca21c7ee2406" />



**We need to install the required plugins for pipeline :**
Terraform , aws-credentials , docker, pipeline:AWS , pipeline-stageview , sonarqube



Once they have installed we need to configure the tools as well


**Terraform** :



<img width="1454" height="528" alt="Screenshot 2026-03-05 at 10 33 18 am" src="https://github.com/user-attachments/assets/d2705b98-fadf-415e-b974-9592ea77ddef" />





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


We need to connect to the cluster and set the context for that we need to configure the AWS Credentials on the jump server with aws configure , here we need to provide
the access key particulars ,so that cluster can be accessed.

<img width="1115" height="154" alt="Screenshot 2026-02-27 at 9 42 01 pm" src="https://github.com/user-attachments/assets/30216689-341f-4cbd-a6d7-77b50adc1f66" />

We can see that can be able to access the cluster with kubectl get nodes .

We want to create loadbalancer controller in kubernetes cluster for that we need a service account ,which can be created with eksctl as below:

The prerequisite for this to configure the aws controller policy as below :

<img width="1343" height="136" alt="Screenshot 2026-02-27 at 10 08 56 pm" src="https://github.com/user-attachments/assets/c2c94238-6461-44f3-ab92-34e6a49ae007" />


Creation of service account as below and can be verified with kubectl get sa -n kubesystem :

<img width="2560" height="1062" alt="Screenshot 2026-02-27 at 10 06 35 pm" src="https://github.com/user-attachments/assets/bd8b96e7-193e-4b2b-95cf-de15b9a38f5e" />

We are installing aws load balancer with helm charts as below :



<img width="867" height="129" alt="Screenshot 2026-02-27 at 10 18 23 pm" src="https://github.com/user-attachments/assets/489799f4-fca7-4065-972e-60093d521df3" />






<img width="1056" height="321" alt="Screenshot 2026-02-27 at 10 21 21 pm" src="https://github.com/user-attachments/assets/5c47bf0b-20e2-472f-993f-3e0d7d3d5698" />







<img width="772" height="114" alt="Screenshot 2026-02-27 at 10 22 33 pm" src="https://github.com/user-attachments/assets/13cd8028-d2f3-445f-a2bd-e0c7f91a94ff" />

Installing ArgoCd operator as below :


<img width="1610" height="75" alt="Screenshot 2026-02-27 at 10 32 07 pm" src="https://github.com/user-attachments/assets/e80ac6a6-1931-4627-b5da-ead92cb9a078" />


We need to configure the argocd service from clsuterIP to loadbalancer , so that we can access the **argocd server** :



<img width="2560" height="1440" alt="Screenshot 2026-02-27 at 10 38 41 pm" src="https://github.com/user-attachments/assets/52988bc0-62e3-4721-8ea9-0b12000d7d5e" />



Accessing argocd server default username is admin and initial password can be accessed from secrets can decoded as below:

echo <password> | base64 --decode

With the password we can login to the arocd server as below :

<img width="2549" height="676" alt="Screenshot 2026-02-27 at 10 48 08 pm" src="https://github.com/user-attachments/assets/18c8e5c9-aac9-43de-9c52-4c8c9f86d5ef" />


Once we configured argocd , next step is to configure the sonarqube , sonarqube can be accessed through port 9000 as below 

<img width="2344" height="815" alt="Screenshot 2026-02-27 at 10 55 10 pm" src="https://github.com/user-attachments/assets/e19f8e15-47e8-4f6c-b5a7-1cefa0a7522a" />

In CI/CD pipeline we will analysis the code , for that jenkins should be integrated with sonarqube and token ,webhooks , projects to be created . 

In jenkins we need to configure the sonar-token as secret text and few variables such as aws account-id,ECR repositories to configured as scerets instead of hardcoding them.configuring tools sonarqube,dpcheck,owasp,nodejs

To push the docker images we will be using aws ecr private registries , since there are 2 we create as front end and backend respositories.These can be referred as secret variables.

Credentials for githb configured in jenkins.Once we configured everything in jenkins such as tools and passwords next step is CI/CD pipeline. 


**b.Continous Integration and Deploying the application through ArgoCD**

Since the application is MERN stack front-end written in nodejs and business logic in react.js using express js ,seperate pipelines for each logic.

**We have implemented frontend pipeline as below :**


<img width="1808" height="560" alt="Screenshot 2026-02-28 at 12 42 33 am" src="https://github.com/user-attachments/assets/dc84a442-ec47-4782-a89f-8ec04cde07ab" />


The image will be pushed to AWS ECR and kubernetes deployment file will be updated with latest build number :

<img width="2210" height="373" alt="Screenshot 2026-02-28 at 12 58 29 am" src="https://github.com/user-attachments/assets/4a7e7316-afa7-4eee-8cb1-61c5e1830936" />


**We have implemented backend pipeline as below :**


<img width="1981" height="737" alt="Screenshot 2026-02-28 at 1 58 31 am" src="https://github.com/user-attachments/assets/2b046f0e-87d1-4fb5-adca-c025aa00098c" />




The image will be pushed to AWS ECR and kubernetes deployment file will be updated with latest build number :

<img width="2240" height="345" alt="Screenshot 2026-02-28 at 2 03 20 am" src="https://github.com/user-attachments/assets/f5757464-810f-43e6-9396-a9c2d76c47cd" />


Once the both pipelines completed successfully , in sonarqube we can see the report as below:


<img width="2527" height="658" alt="Screenshot 2026-02-28 at 1 22 32 am" src="https://github.com/user-attachments/assets/b557a57b-dfda-417b-8c03-6e4e8f316b56" />



The next step is to configure deployment  in Argo CD :

We have created seperate deployments for database,front-end,back-end and ingress 


<img width="1728" height="962" alt="Screenshot 2026-03-05 at 5 40 23 pm" src="https://github.com/user-attachments/assets/59282e82-40e3-4770-b029-64f6f9781c90" />







We can see that all resources created in the three-tier namespace
<img width="923" height="484" alt="Screenshot 2026-03-05 at 7 33 30 pm" src="https://github.com/user-attachments/assets/3d0e29ac-d5c9-4b0e-9050-e891260a3ffb" />







We have our custom domain with that we need to create a hosted zone and map the record to created kubernetes loadbalancer.
**It is seen that domain is mapped to aws loadbalancer** :



<img width="1299" height="120" alt="Screenshot 2026-03-05 at 7 54 51 pm" src="https://github.com/user-attachments/assets/c1826f43-f39f-4830-83b5-8fe760d2fbb1" />

**if we navigate to our domain we can see that our we can able to access the application Hurray !!!!!**


<img width="1728" height="1087" alt="Screenshot 2026-03-05 at 7 47 05 pm" src="https://github.com/user-attachments/assets/ed16cd11-4968-4973-baac-1138638b497b" />


**If we try to access the backend as well we can access as below :**



<img width="1452" height="140" alt="Screenshot 2026-03-05 at 7 52 30 pm" src="https://github.com/user-attachments/assets/e3bc4ed2-2b2f-41ec-957d-0802593d082c" />


**Configuring Monitoring :**
We will use prometheus and grafana for monitoring and observability as below :

Prometheus installed using helm charts

helm repo add stable https://charts.helm.sh/stable
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm install prometheus prometheus-community/prometheus

**Prometheus can be accessed by exposing prometheus service to loadbalancer:**
<img width="1724" height="1081" alt="Screenshot 2026-03-05 at 10 39 22 pm" src="https://github.com/user-attachments/assets/421322d5-e463-441e-bcb9-8ca58e3a66f7" />






**Configuring Observability :**
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
helm install grafana grafana/grafana

<img width="1470" height="860" alt="Screenshot 2026-03-05 at 10 47 10 pm" src="https://github.com/user-attachments/assets/c027f77e-c51d-45af-9c17-00809888b492" />


We have configured the prometheus in grafana dashboard by adding loadbalancer dns of prometheus service

<img width="1727" height="1007" alt="Screenshot 2026-03-05 at 10 53 08 pm" src="https://github.com/user-attachments/assets/70694ce6-1d68-4442-9824-2929e8fd3733" />



**We can access the dashboards as below:**
<img width="1728" height="1117" alt="Screenshot 2026-03-05 at 10 55 41 pm" src="https://github.com/user-attachments/assets/f8e07142-8dea-4ac6-85a8-9c6a1ae4dc61" />


The MERN stack end-to-end devsecops and monitoring & obersvabilty project completed.
