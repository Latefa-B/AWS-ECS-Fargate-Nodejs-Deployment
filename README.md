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
Install Docker and Git using those commands : 
sudo yum update -y
sudo yum install docker git -y
sudo service docker start
sudo usermod -aG docker ec2-user
exit  # Then reconnect
<img width="1056" height="518" alt="8" src="https://github.com/user-attachments/assets/01393ae6-ff2d-45f3-b791-7c9e683e4036" />

<img width="1049" height="465" alt="9" src="https://github.com/user-attachments/assets/fe3fc82c-aced-4afd-b8dd-195539a512fc" />

Now that Docker and Git are installed successfully on the EC2 Instance, Let’s start building the Application !
