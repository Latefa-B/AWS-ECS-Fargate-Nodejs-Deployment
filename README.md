# Step-by-step guide to Deploy a Node.js App on AWS using ECS Fargate and ECR 
Deploying a Node.js App on AWS with ECS Fargate and ECR, involves building and deploying a scalable, cost-efficient and secure Application over the Cloud without provisioning or managing any servers. It involves : 

- Dockerizing and building the Application.
- Pushing it to Amazon ECR for secure storage.
- Deploying the Application and running it in a secure, scalable environment on ECS for orchestrating container deployments.
- Using Fargate with ECS, as a serverless compute engine that manages the underlying infrastructure.

In this project, I will walk you through the steps to build, push, deploy, and monitor a basic containerized application (Node.js app), using AWS like : **EC2, ECR, ECS, Fargate, IAM and CloudWatch**. I will break down the process in 5 phases. We will see step-by-step, how to : 

- Set up EC2 as a workstation to build and push a Docker image.
- Build a Docker image.
- Push the image to Amazon private registry : Elastic Container Registry (ECR).
- Deploy it using a serverless compute engine : Amazon Elastic Container Service (ECS) with Fargate. And set up the required IAM permissions.
- Monitor and Test the Application with CloudWatch logs and Access it on the Web Browser via Public IP Address.

### Design of the Application’s Architecture on AWS
<img width="756" height="647" alt="Screenshot 2025-05-17 at 5 28 33 PM" src="https://github.com/user-attachments/assets/79da3a22-3b5b-4046-95b2-c79c5fbf5c55" />


### Workflow of the Application on AWS using EC2, ECR, ECS, IAM and CloudWatch services 
<img width="546" height="640" alt="Screenshot 2025-05-18 at 7 48 01 PM" src="https://github.com/user-attachments/assets/27887fbc-b980-4707-980b-02435397e41c" />

## Phase 1: Set Up EC2 as Your Workstation
**Step 1: Launch EC2 Instance**
- Go to EC2 Console → **Launch Instance**
<img width="1454" height="383" alt="1" src="https://github.com/user-attachments/assets/beca01f0-f8ea-4e89-95e0-0192fbd4fdfe" />

- Name the instance : **lab3-ec2-workstation**
- Specify the OS: **Amazon Linux 2**
<img width="1410" height="710" alt="2" src="https://github.com/user-attachments/assets/ac658df1-82f2-4da9-a54f-dcbd181bbbd1" />

- Specify the Type : **t2.micro (Free Tier)**
- Specify a Key Pair : **Create new or use existing**
<img width="1437" height="600" alt="3" src="https://github.com/user-attachments/assets/21b878cb-48ed-48d6-8225-4af3cea1f429" />

- Enable SSH on port 22 : **Check Allow SSH traffic From and specify your IP Address**.
<img width="1408" height="630" alt="4" src="https://github.com/user-attachments/assets/631a7c68-a04e-4dae-99a7-5556b96a5418" />

- Click **Launch instance**
<img width="1460" height="282" alt="5" src="https://github.com/user-attachments/assets/2bfb6f0f-b82c-4c6c-b46a-4fc560057cec" />

**Step 2: Connect to EC2**
- Connect to the EC2 instance using SSH from your terminal : ssh -i "your-key.pem" ec2-user@<your-ec2-public-ip>
<img width="1444" height="594" alt="6" src="https://github.com/user-attachments/assets/3a3f335c-84af-4a45-98ae-0bec66307542" />

<img width="847" height="267" alt="7" src="https://github.com/user-attachments/assets/e23a159f-a29c-428a-89f9-916a04db14c8" />

**Step 3: Install Docker & Git**
- Install Docker and Git using those commands : 
sudo yum update -y
sudo yum install docker git -y
sudo service docker start
sudo usermod -aG docker ec2-user
exit  # Then reconnect
<img width="1056" height="518" alt="8" src="https://github.com/user-attachments/assets/01393ae6-ff2d-45f3-b791-7c9e683e4036" />

<img width="1049" height="465" alt="9" src="https://github.com/user-attachments/assets/fe3fc82c-aced-4afd-b8dd-195539a512fc" />

Now that Docker and Git are installed successfully on the EC2 Instance, Let’s start building the Application !

## Phase 2: Build the Node.js App
**Step 4: Clone the App from GitHub**
- Generate a new SSH key on the instance using the command ssh-keygen
<img width="747" height="321" alt="1" src="https://github.com/user-attachments/assets/3f1b36e7-d7a6-4ccc-9a38-9d5fcd9f4066" />

- Locate the public key and Copy it
- On the github platform, Navigate to your settings
- Go to SSH and GPG keys: In the sidebar, and click on **"SSH and GPG keys"**
- Click **"New SSH key"** , paste the new SSH key
- specify a description and click on **"Add SSH key"**. 
<img width="1304" height="699" alt="2" src="https://github.com/user-attachments/assets/7dda21ec-828b-4a3b-8ddb-bdde7cb7c62b" />

- on the terminal, add the new ssh key to ssh agent and authenticate to your Github profile.
- start the ssh-agent and add your private key to it using the command : ssh-add
<img width="659" height="103" alt="3" src="https://github.com/user-attachments/assets/14152e2f-9417-419e-af12-6af958b6e0e7" />

- Clone the remote repository to import the web application code using the command : git clone <ssh-url>
- cd into the folder : **aws-node-app-lab3**
<img width="982" height="235" alt="4" src="https://github.com/user-attachments/assets/fe05d256-73d6-42e0-860e-0626267af23a" />

**Step 5: Build Docker Image**
- Locate the Dockerfile inside the folder and build the Docker image using the command : docker build -t aws-node-app-lab3 .
<img width="994" height="534" alt="5" src="https://github.com/user-attachments/assets/9ec0d514-51e4-42a6-a040-913a98d193e9" />
<img width="996" height="265" alt="6" src="https://github.com/user-attachments/assets/5c850467-1de1-48a8-b3f4-0728dc8bea64" />

## Phase 3: Push Image to Amazon ECR
**Step 6: Create ECR Repository (Console)**
- Go to ECR → **Create Repository**
<img width="1420" height="754" alt="1" src="https://github.com/user-attachments/assets/95817760-1ba8-4cef-ac26-bee20702460e" />

- On Amazon ECR → Private registry → Repositories → Specify the settings : 
- Name :  **aws-node-app-lab3**
- Visibility : **Private**
- Leave other settings as default
<img width="1424" height="694" alt="2" src="https://github.com/user-attachments/assets/42084342-bb1b-4925-89da-5d1e6c25f5c9" />
<img width="1427" height="567" alt="3" src="https://github.com/user-attachments/assets/49c78cd5-8ead-403e-8c3d-e7a6777ac8dd" />


- Click **Create**
<img width="1421" height="468" alt="4" src="https://github.com/user-attachments/assets/182a0ec3-585b-40ca-905a-39c8d7a57624" />

**Step 7: Authenticate and Push from EC2**
- In order to be able to authenticate to the ECR registry and Push the Docker image to it, we need beforehand to : 
- Install aws CLI on the EC2.
- Configure aws credentials of the user on EC2.
<img width="979" height="588" alt="5" src="https://github.com/user-attachments/assets/0b108746-c0ba-4f8e-a646-5eb8024848ea" />


- Install and Configure the aws CLI on the EC2 Instance : 
- Install aws CLI on the EC2 using the command :  curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip" unzip awscliv2.zip sudo ./aws/install

<img width="1036" height="186" alt="6" src="https://github.com/user-attachments/assets/a454f78c-c59c-4482-9e23-9e4e14933111" />




- Configure aws credentials of the user on EC2
- Go to the IAM Dashboard → Users → your-user-name → use an existing access key or create a new one.

<img width="1442" height="695" alt="7" src="https://github.com/user-attachments/assets/440a8a75-0369-4edd-ace3-2282c71fb167" />



- Configure AWS user’s credentials using the command : **aws configure.**

<img width="891" height="71" alt="8" src="https://github.com/user-attachments/assets/05765dd3-d26e-4a70-b70f-fa07f93fe383" />


- Authenticate and push the Docker Image from the EC2 to the ECR repository : 
-  Authenticate the Docker client to to the EC2 from your terminal using the command :  aws ecr get-login-password --region us-east-1 | \\ docker login --username AWS--password-stdin <your-account-id>.dkr.ecr.us-east-1.amazonaws.com

<img width="1047" height="96" alt="9" src="https://github.com/user-attachments/assets/d4200fd8-faae-4a8b-9fdc-8d369beef446" />


- Push the Docker image from the EC2 to the ECR private registry using the command : docker tag aws-node-app-lab3:latest <your-ecr-url>/aws-node-app-lab3:latest docker push <your-ecr-url>/aws-node-app-lab3:latest

<img width="997" height="256" alt="10" src="https://github.com/user-attachments/assets/3d0aa2cb-f302-4fb2-b599-f21f9bae43da" />

- Check the Docker image on the ECR Registry : 
- Go to AWS console  → Amazon ECR →  Private registry → Repositories 
- Check inside the **aws-node-app-lab3** repository.

<img width="1464" height="383" alt="11" src="https://github.com/user-attachments/assets/0569365f-379b-41fa-b2c5-8ea332105179" />


Now that the Docker image was pushed successfully, let’s deploy the Application !

## Phase 4: Deploy Using ECS Fargate
**Step 8 : Create ECS Cluster**
- Go to ECS → Clusters → Get Started → **Create Cluster**

<img width="1440" height="734" alt="1" src="https://github.com/user-attachments/assets/4a21dbd4-c05d-4f8d-9424-2a13453a7cb6" />

<img width="1458" height="366" alt="2" src="https://github.com/user-attachments/assets/c5c1246c-850e-4819-8b6b-26b5e1ebad69" />

- Specify the Cluster settings : 
- Name it : **lab3-ecs-cluster**
- Choose **FARGATE**
<img width="1440" height="664" alt="3" src="https://github.com/user-attachments/assets/fad176db-6fc2-4653-aad7-09f641c728b8" />

- Click **Create**
<img width="1215" height="357" alt="4" src="https://github.com/user-attachments/assets/838dd8b3-d194-4ea8-ac84-3e7f67bb88b9" />
<img width="1437" height="331" alt="5" src="https://github.com/user-attachments/assets/d593738a-8b97-42a1-b025-59256548ea87" />

**Step 9 : Create IAM Role for ECS**
- Go to IAM → Roles → Create Role → select AWS service
<img width="1424" height="483" alt="6" src="https://github.com/user-attachments/assets/fa44e3ec-0306-42ae-9076-d348616c9a15" />

- In use case, choose : **Elastic Container Service Task**
<img width="1450" height="737" alt="7" src="https://github.com/user-attachments/assets/1058fd6e-585b-4977-887f-9739acd32141" />

- Name the role : **ecsTaskExecutionRole**


- Attach these policies: **AmazonECSTaskExecutionRolePolicy** and **CloudWatchLogsFullAccess**



- Click **Create role**
<img width="1446" height="477" alt="8" src="https://github.com/user-attachments/assets/247235a8-04e2-43ee-aed6-a1a92701dff4" />


<img width="1437" height="687" alt="10" src="https://github.com/user-attachments/assets/a79dc1d5-9767-4125-80c5-0079b5cc12d7" />
<img width="1456" height="717" alt="9" src="https://github.com/user-attachments/assets/c8bad476-daf9-4b01-b594-57773b638cfa" />
![Uploading 10.png…]()



**Step 10 : Register Task Definition**
- Go to ECS → Task Definitions → Click **Create new task definition**

- Specify the task definition settings : 
- Name: **lab3-task**
- Select Launch Type: **FARGATE**

- Specify the task execution role previously created : **ecsTaskExecutionRole**

- Add Container :
- Name: **aws-node-app-lab3**
- Image: **<your-ecr-url>/aws-node-app-lab3:latest**
- Port: **3000**

- Enable Logging : **check Log Collection**
- Specify : **Amazon Cloudwatch**
- Specify the following settings : 
- Log driver: **awslogs**
- Log group: **/ecs/lab3-logs**
- Region: **us-east-1**
- Prefix: **ecs**


- Leave other settings as default


- Click **Create**






**Step 11: Create ECS Service**
- Go to ECS → Clusters → select your cluster : **lab3-ecs-cluster**  → Services 
- Click **Create** 

- Specify the service settings : 
- Select Task Definition family : **lab3-task**
- Task Definition revision : **1**
- Service Name: **lab3-service**

- Specify the environment settings : 
- Launch Type: **FARGATE**
- Platform version : **LATEST**

- Desired Count: **1**

- Specify the Network settings :
- Use default VPC
- Select public subnets
- Use an existing security group or create a new one
- Turn On public IP


- Leave other settings as default







- Click **Create**
<img width="1437" height="379" alt="11" src="https://github.com/user-attachments/assets/438cfbe6-cb9a-42a0-8848-596f9f290382" />
<img width="1439" height="535" alt="12" src="https://github.com/user-attachments/assets/192ee18e-b49e-43eb-82d5-47586f86d5b1" />
<img width="1344" height="746" alt="13" src="https://github.com/user-attachments/assets/8d833603-f624-45d2-8f9a-6a8bc18955d3" />
<img width="1345" height="773" alt="14" src="https://github.com/user-attachments/assets/44fcbb08-3a3a-4962-9b2f-eaec2756e9de" />
<img width="1450" height="663" alt="15" src="https://github.com/user-attachments/assets/b34b4b12-f7b3-4bef-9a64-f5424baa22db" />
<img width="1440" height="729" alt="16" src="https://github.com/user-attachments/assets/aaab2913-0456-44da-8bba-348697ad3d6b" />
<img width="1441" height="669" alt="17" src="https://github.com/user-attachments/assets/4e8c40ff-4f9e-4da7-9634-f9aeb951adf3" />
<img width="1447" height="689" alt="18" src="https://github.com/user-attachments/assets/47c87579-70ad-4b65-bfea-1c94f71735ae" />
<img width="1430" height="504" alt="19" src="https://github.com/user-attachments/assets/bbc14b72-1601-4333-8f9f-9444dddb4328" />



<img width="1433" height="571" alt="20" src="https://github.com/user-attachments/assets/24d972fa-f1b6-4f1b-aec4-243fc277bda8" />

<img width="1443" height="658" alt="21" src="https://github.com/user-attachments/assets/8229218d-d4b7-4498-b001-d7381c43fd56" />
<img width="1443" height="745" alt="22" src="https://github.com/user-attachments/assets/c5ff4ccf-3c2a-4d12-bee2-b8544d2989c8" />
<img width="1437" height="732" alt="23" src="https://github.com/user-attachments/assets/e7bcea43-dfa6-4829-b5bb-efac7b3b2758" />
<img width="1364" height="643" alt="24" src="https://github.com/user-attachments/assets/84cf525f-34d6-401a-88b2-e684b4917c59" />


## Phase 5: Monitor & Test
**Step 12: Check Logs in CloudWatch**
- Go to CloudWatch → Log groups → /ecs/lab3-logs

- Click on  /ecs/lab3-logs and check the last log stream

**Expected output** : You should see log output : App running on port 3000


**Step 13: Configure Security Groups on Port 3000 before testing the application**
Now that we are done deploying the application, the last step before testing the application on the web browser is to configure the Security Groups.. For that we will allow traffic on Port 3000, on the EC2 Instance security group and the ECS Task  security group.

**- EC2 Instance Security Group :**
- Go to EC2 Dashboard → instances →  your EC2 instance → security → Click security groups → Inbound rules → Click Edit inbound rules.
- Add rule Custom TCP on Port 3000 to allow traffic on that port.
- Click save rules.



**- ECS Task  security group :** 
Go to ECS Dashboard → Clusters → select your cluster : lab3-ecs-cluster → Tasks → your task name → Networking → Click on the security group associated with your ECS task.



- On Inbound rules → Click Edit inbound rules → Click Add rule.
- Add rule Custom TCP on Port 3000 to allow traffic on that port.
- Click save rules.



**Step 14 : Access the App in the Web Browser**
- Go to ECS → Clusters →  select your cluster →  Tasks →  Click your running task 

- Inside the running task, Configuration →  Get Public IP Address.

- Open: http://<public-ip>:3000
- Test the application. The Expected Output is : **Hello from AWS Lab 3!**



<img width="1438" height="460" alt="1" src="https://github.com/user-attachments/assets/b8e3e27c-5a71-4a4b-9a94-0549cce6d866" />

<img width="1433" height="492" alt="2" src="https://github.com/user-attachments/assets/829f22c9-e0b1-4eba-85cd-0af924c642ff" />

<img width="1429" height="396" alt="3" src="https://github.com/user-attachments/assets/983615ec-863a-404c-9aa3-5cb82552ca48" />

<img width="1422" height="594" alt="4" src="https://github.com/user-attachments/assets/6383b447-6976-4f07-b4b5-bbd73bccf023" />

<img width="1428" height="536" alt="5" src="https://github.com/user-attachments/assets/992ebbbb-ede7-41fd-9741-4d7c76061ded" />

<img width="1424" height="599" alt="6" src="https://github.com/user-attachments/assets/1201c20a-151c-45c0-882e-ac33bdc65a55" />

<img width="1412" height="696" alt="7" src="https://github.com/user-attachments/assets/c2865bdc-9da4-4fbc-a631-d2a139af869e" />

<img width="1316" height="323" alt="8" src="https://github.com/user-attachments/assets/e88c557f-7341-4beb-a746-7f92ecb131b5" />

## Summary
This breakdown provides a step-by-step guide on how to deploy a basic node.js application using the AWS services: 
- EC2 as a workstation to dockerize and build the Application.
- ECR for secure storage.
- ECS for orchestrating container deployments.
- Fargate with ECS, as a serverless compute engine.
- CloudWatch for monitoring the application’s logs.
- IAM to set up permissions

It also provides an overview of how those services function together, from building and Dockerizing a Node.js app to pushing the image to Amazon ECR, deploying it using Amazon ECS with Fargate and viewing logs in CloudWatch. 
In summary, the purpose of this project was to walk you through the process of a basic containerized application that will be hosted in a private registry (ECR), deployed on a fault-tolerant, auto-scalable environment using ECS Fargate, and accessible over the internet but also, securely within a private network. 
