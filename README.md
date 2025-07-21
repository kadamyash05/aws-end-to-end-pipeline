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
  - <img width="940" height="125" alt="image" src="https://github.com/user-attachments/assets/4a48217f-7bf5-4bb6-8115-a0d7a17ab2da" />


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

---

## 🧪 Triggering the Pipeline

Pushing any changes to the `main` branch of your GitHub repo will automatically:

1. Install dependencies
2. Build the Docker image
3. Authenticate and push the image to your Docker Hub repo

The image will be available at your docker hub repository
<img width="940" height="319" alt="image" src="https://github.com/user-attachments/assets/d522f86d-d646-460c-a46b-b62ea25c4f84" />

