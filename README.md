# AWS CI/CD Pipeline: Docker Image Build & Push to Docker Hub

This project sets up an automated CI/CD pipeline using **AWS CodePipeline** and **CodeBuild** to build a Docker image from your Python app and push it to Docker Hub.

## 🔧 Project Structure

- `Dockerfile` — Defines the Flask app image
- `requirements.txt` — Python dependencies
- `buildspec.yml` — Instructions for CodeBuild (install, build, push)
- `app.py` - Sample python app
- `buildspec.yml`- For your reference (required for code build)

---

## ✅ Prerequisites

Before starting, make sure you have:

- ✅ A sample Python project with a `Dockerfile` and `requirements.txt`
- ✅ A Docker Hub account and profile with login credentials
- ✅ The project code pushed to a public/private GitHub repository

---

## ✅ Steps to Set Up Pipeline

### 1. **Store Secrets in AWS Systems Manager Parameter Store**

Go to **AWS Systems Manager > Parameter Store** and add the following:

Edit the details as per your credentials

<img width="940" height="316" alt="image" src="https://github.com/user-attachments/assets/04196ce7-caeb-46b0-aad8-b3c68427abc2" />



---

### 2. **Create IAM Role for CodeBuild**

Go to **IAM > Roles** and create a new role:

- **Trusted entity**: AWS service → CodeBuild
- **Attach policies**:
  - `AmazonSSMReadOnlyAccess`
  <img width="1524" height="689" alt="image" src="https://github.com/user-attachments/assets/d8c2a2d4-c075-45e3-bb20-ae46f0015a78" />



---

### 3. **Create a CodeBuild Project**

- **Source provider**: GitHub
- **Connect repo**: Choose your GitHub project
- **Environment**:
  - Runtime: Ubuntu, standard image
  - **Enable** `privileged` mode (required for Docker)
  - Use the IAM role created above
- Use the editor to create a build spec file, you can refer to the buildspec.yml in this repo
- <img width="940" height="504" alt="image" src="https://github.com/user-attachments/assets/03c5b6bd-4f6a-4db0-b02f-3f27d53942b8" />
<img width="1420" height="784" alt="image" src="https://github.com/user-attachments/assets/687d8766-948b-4b6f-be85-195fc6ea0a5b" />


---

### 4. **Create a CodePipeline**

- **Source**:
  - Provider: GitHub
  - Select your repository and branch
- **Build**:
  - Provider: CodeBuild
  - Select the CodeBuild project created in step 3
<img width="1445" height="331" alt="image" src="https://github.com/user-attachments/assets/a0be06df-db2e-44bf-9cbe-76a457c1c38a" />

- **How the pipeline will look -**

<img width="940" height="319" alt="image" src="https://github.com/user-attachments/assets/d522f86d-d646-460c-a46b-b62ea25c4f84" />
---

## 🧪 Triggering the Pipeline

Pushing any changes to the `main` branch of your GitHub repo will automatically:

1. Install dependencies
2. Build the Docker image
3. Authenticate and push the image to your Docker Hub repo

The image will be available at your docker hub repository:
<img width="940" height="199" alt="image" src="https://github.com/user-attachments/assets/f61cb7d7-e6d8-4628-ab80-801b264a4475" />

# 🚀 Deploy Docker Image to EC2 via CodeDeploy

## Prerequisites
Make sure your repository contains:
- `appspec.yml`
- `scripts/deploy.sh`

---

## Step 5: Create IAM Role and Attach to EC2
- Go to IAM > Roles > Create Role
- Trusted entity: AWS service > EC2
- Attach policies:
  - AmazonEC2RoleforAWSCodeDeploy
  - AmazonSSMManagedInstanceCore
- Name the role (e.g., EC2CodeDeployRole) and create it
- Go to EC2 > Instances > Select instance > Actions > Security > Modify IAM Role > Attach the above role
- 
- <img width="940" height="444" alt="image" src="https://github.com/user-attachments/assets/cede69b5-06cd-4942-89c8-f282ebdf41f4" />v
---

## Step 6: Install Docker and CodeDeploy Agent on EC2

```bash
sudo apt update
sudo apt install ruby-full
sudo apt install wget
cd /home/ubuntu
wget https://bucket-name.s3.region-identifier.amazonaws.com/latest/install
chmod +x ./install
sudo ./install auto
systemctl status codedeploy-agent
systemctl start codedeploy-agent
```

---

## Step 7: appspec.yml (place in project root)

## Step 8: scripts/start_container.sh (refer repo)

## Step 9: scripts/start_container.sh (refer repo)

## Step 6: Create CodeDeploy Application and Deployment Group

- Go to AWS CodeDeploy > Applications > Create Application
- Platform: EC2/On-Premises
- Name: `myapp-deploy`

Then create Deployment Group:
- Name: `myapp-deploy-group`
- Service role: (IAM role for CodeDeploy, e.g., CodeDeployServiceRole with `AWSCodeDeployRole`)
- Deployment type: In-place
- Environment configuration: Amazon EC2 instances
- Add EC2 instance tag (e.g., Key: Name, Value: MyInstance)
- Deployment settings: CodeDeployDefault.AllAtOnce
- Save

- <img width="940" height="533" alt="image" src="https://github.com/user-attachments/assets/bfd96fb9-89cb-4b35-a791-091c6ba3de5a" />

<img width="940" height="436" alt="image" src="https://github.com/user-attachments/assets/7e90a382-3561-451e-ac5d-e4a272294b49" />



---

## Step 6: Trigger Deployment

Whenever you push changes to GitHub, your pipeline will build the image, push it to Docker Hub, and trigger CodeDeploy, which pulls and runs the latest Docker image on EC2.


<img width="940" height="356" alt="image" src="https://github.com/user-attachments/assets/3eabfc5b-fb3d-4fb4-8b4b-e938b4c690ac" />

