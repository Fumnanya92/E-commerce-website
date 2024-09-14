# Automating Deployment of E-commerce Website: CI/CD Mastery

### Project Scenario

A technology consulting firm is migrating to cloud architecture. My task as a DevOps Engineer is to design and implement a CI/CD pipeline using **Jenkins** to automate the deployment of an e-commerce web application. This pipeline will handle **continuous integration (CI)** and **continuous deployment (CD)**, ensuring scalability and reliability.

---

### Pre-requisites
- **Knowledge of Jenkins essentials**
- Completion of:
  - Introduction to Jenkins
  - Jenkins Freestyle Project
  - Jenkins Pipeline Job mini projects

---

### Project Deliverables

1. **Documentation:**
   - Detailed setup of Jenkins components
   - Security measures at every stage

2. **Demonstration:**
   - Live demo of the complete CI/CD pipeline.

---

## Project Components

### 1. Jenkins Server Setup

#### Objective:
Configure a Jenkins server to automate the CI/CD pipeline.

#### Steps:

##### 1.1 Install Jenkins on a Dedicated Server
```bash
# For Ubuntu server
sudo apt update
sudo apt install default-jdk-headless
wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > \
/etc/apt/sources.list.d/jenkins.list'
sudo apt update
sudo apt-get install jenkins

# Start Jenkins if need be 
sudo systemctl start jenkins
sudo systemctl enable jenkins

![image](https://github.com/user-attachments/assets/7bdec00c-58e8-4898-8dc9-5b35465cd0ab)


# Access Jenkins via the browser
http://<your_server_ip>:8080
```
- **Unlock Jenkins**: The initial admin password is located in `/var/lib/jenkins/secrets/initialAdminPassword`.

##### 1.2 Set Up Necessary Plugins
- After logging into Jenkins, install the following essential plugins:
  - **Git plugin**: Enables Git integration for pulling code.
  - **Docker plugin**: Allows Jenkins to run Docker containers for builds and deployments.
  - **Blue Ocean Plugin**: enriches your website building experience by adding extra features to the OceanWP free theme.

  ```bash
  # Navigate to "Manage Jenkins" > "Manage Plugins" > "Available Plugins"
  # Install Git, Docker Pipeline, Pipeline plugins, Blue Ocean Plugins
  ```
![image](https://github.com/user-attachments/assets/4fe1d73f-e5cf-4ec6-a6d2-fe6f76f4e1c8)



##### 1.3 Installing Docker to the machine
-  `nano` into `docker.sh` and paste the follwing script
  
  ```
  sudo apt-get update -y
sudo apt-get install ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update -y
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y
sudo systemctl status docker
```
- Make the file executable
  ```
  chmod +x docker.sh
  ```
- Run the file to install docker
  ```
  ./docker.sh
  ```
  ![image](https://github.com/user-attachments/assets/cc24cc00-3e14-4ad3-bb62-1d4a37a09a8f)

---

### 2. Source Code Management Repository Integration
##### 2.1 Integrate Jenkins with GitHub
- Go to **Manage Jenkins** > **Configure System**.
- Scroll down to **GitHub** section and add your **GitHub credentials** (username, password, or access token).
  
##### 2.2 Configure Webhooks for Automatic Triggering of Jenkins Builds
- **Webhook Setup on GitHub**:
  - Go to the GitHub repository.
  - Click **Settings** > **Webhooks** > **Add webhook**.
  - Set the **Payload URL** to `http://<jenkins-server-ip>:8080/github-webhook/`.
  - set the **Content type** to application/json
  - Choose **Just the push event** to trigger builds on every commit. Save
  ![image](https://github.com/user-attachments/assets/06974e86-6ac1-47fd-b343-49a36db15dbb)

  
```bash
# GitHub webhook URL example
http://<jenkins-server-ip>:8080/github-webhook/
```
![image](https://github.com/user-attachments/assets/b650d450-c5aa-465f-858a-a719bdae5f8e)

---

### 3. Jenkins Freestyle Jobs for Build and Unit Tests

#### Objective:
To create Jenkins Freestyle jobs for building the web application and running unit tests.

#### Steps:

##### 3.1 Set Up a Freestyle Job for Building the Application

1. Navigate to **New Item** > **Freestyle Project**.
2. **Source Code Management**: Select **Git** and enter the repository URL.
3. **Branches to build**: change this to */main

4. **Build Trigger**: Select **GitHub hook trigger for GITScm polling**.
- Save.


---

### 4. Jenkins Pipeline for Web Application

#### Objective:
To develop a Jenkins Pipeline for running the e-commerce web application.

#### Steps:

##### 4.1 Create a Jenkins Pipeline Script to Run the Application

1. Go to **New Item** > **Pipeline**.
   ![image](https://github.com/user-attachments/assets/c0debe04-7fd0-4f08-bf6d-aa8d4c0fc98d)

3. **Pipeline Definition**: Choose **Pipeline script from SCM** and provide the GitHub repository URL.
   ![image](https://github.com/user-attachments/assets/1c64422a-bec6-47ea-b3a4-ece5cc3f159e)

5. **Pipeline Script Example**:

```groovy
pipeline {
    agent any

    stages {
        stage('Connect To Github') {
            steps {
                    checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/RidwanAz/jenkins-scm.git']])
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    sh 'docker build -t dockerfile .'
                }
            }
        }
        stage('Run Docker Container') {
            steps {
                script {
                    sh 'docker run -itd -p 8081:80 dockerfile'
                }
            }
        }
    }
}

```
- **Checkout Stage**: Pulls the latest code from the repository.
- **Build Stage**: Builds the application (dockerfile).
- **Test Stage**: Runs unit tests to ensure code stability. - this stage is not in the above script 
- **Deploy Stage**: Executes deployment logic (e.g., Docker container or Kubernetes deployment).
---

![image](https://github.com/user-attachments/assets/b43874fd-3493-450e-82c3-71ac88e9661c)



This documentation outlines the steps needed to automate the deployment of an e-commerce website using Jenkins CI/CD pipeline, ensuring a reliable and scalable environment.
