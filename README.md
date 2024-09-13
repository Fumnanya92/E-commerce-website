# Automating Deployment of E-commerce Website: CI/CD Mastery

### Project Scenario

A technology consulting firm is migrating to cloud architecture. Your task as a DevOps Engineer is to design and implement a CI/CD pipeline using **Jenkins** to automate the deployment of an e-commerce web application. This pipeline will handle **continuous integration (CI)** and **continuous deployment (CD)**, ensuring scalability and reliability.

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
To configure a Jenkins server for automating the CI/CD pipeline.

#### Steps:

##### 1.1 Install Jenkins on a Dedicated Server
```bash
# For Ubuntu server
sudo apt update
sudo apt install openjdk-11-jdk -y
wget -q -O - https://pkg.jenkins.io/debian/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
sudo apt update
sudo apt install jenkins -y

# Start Jenkins
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

  ```bash
  # Navigate to "Manage Jenkins" > "Manage Plugins" > "Available Plugins"
  # Install Git, Docker Pipeline, Pipeline plugins
  ```

![image](https://github.com/user-attachments/assets/cc24cc00-3e14-4ad3-bb62-1d4a37a09a8f)


##### 1.3 Configure Jenkins with Required Security Measures
- **User Authentication**: 
  - Go to "Manage Jenkins" > "Configure Global Security".
  - Set up **Jenkins own user database** or integrate with LDAP/AD if applicable.
- **Role-based Access Control**:
  - Install the **Role-based Authorization Strategy** plugin.
  - Create roles for admins, developers, and others with specific access rights.
- **CSRF Protection**:
  - Enable Cross-Site Request Forgery protection from the global security configuration.
- **SSL Setup**: 
  - For production environments, configure SSL using a reverse proxy (Nginx or Apache) to protect Jenkins traffic.
![image](https://github.com/user-attachments/assets/4fe1d73f-e5cf-4ec6-a6d2-fe6f76f4e1c8)

---

### 2. Source Code Management Repository Integration

#### Objective:
To integrate Jenkins with a source code management system (e.g., GitHub) for tracking changes in the project.

#### Steps:

##### 2.1 Integrate Jenkins with GitHub
- Go to **Manage Jenkins** > **Configure System**.
- Scroll down to **GitHub** section and add your **GitHub credentials** (username, password, or access token).
  
##### 2.2 Configure Webhooks for Automatic Triggering of Jenkins Builds
- **Webhook Setup on GitHub**:
  - Go to the GitHub repository.
  - Click **Settings** > **Webhooks** > **Add webhook**.
  - Set the **Payload URL** to `http://<jenkins-server-ip>:8080/github-webhook/`.
  - Choose **Just the push event** to trigger builds on every commit.
  
```bash
# GitHub webhook URL example
http://<jenkins-server-ip>:8080/github-webhook/
```

---

### 3. Jenkins Freestyle Jobs for Build and Unit Tests

#### Objective:
To create Jenkins Freestyle jobs for building the web application and running unit tests.

#### Steps:

##### 3.1 Set Up a Freestyle Job for Building the Application

1. Navigate to **New Item** > **Freestyle Project**.
2. **Source Code Management**: Select **Git** and enter the repository URL.
3. **Build Trigger**: Select **GitHub hook trigger for GITScm polling**.
4. **Build Steps**:
   - Add **Execute Shell** and configure the build process (e.g., `mvn clean install` for Java-based projects).
5. **Post-build Actions**:
   - Add **Publish JUnit test result report** for unit tests.

```bash
# Example build command for Maven-based Java app
mvn clean install
```

---

### 4. Jenkins Pipeline for Web Application

#### Objective:
To develop a Jenkins Pipeline for running the e-commerce web application.

#### Steps:

##### 4.1 Create a Jenkins Pipeline Script to Run the Application

1. Go to **New Item** > **Pipeline**.
2. **Pipeline Definition**: Choose **Pipeline script from SCM** and provide the GitHub repository URL.
3. **Pipeline Script Example**:

```groovy
pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/your-repository-url.git', branch: 'main'
            }
        }
        
        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }
        
        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }
        
        stage('Deploy') {
            steps {
                echo 'Deploying to production...'
                // Add deployment steps (e.g., using Docker or Kubernetes)
            }
        }
    }
    
    post {
        success {
            echo 'Build completed successfully!'
        }
        failure {
            echo 'Build failed!'
        }
    }
}
```
- **Checkout Stage**: Pulls the latest code from the repository.
- **Build Stage**: Builds the application (e.g., using Maven).
- **Test Stage**: Runs unit tests to ensure code stability.
- **Deploy Stage**: Executes deployment logic (e.g., Docker container or Kubernetes deployment).

---

## Security Measures Summary
- **User Authentication**: Implemented Jenkins user database or LDAP.
- **Role-based Access Control**: Users are assigned specific roles and permissions.
- **CSRF Protection**: Enabled by default in Jenkins settings.
- **SSL**: Configured via reverse proxy for secure communication.

---

This documentation outlines the steps needed to automate the deployment of an e-commerce website using Jenkins CI/CD pipeline, ensuring a reliable and scalable environment.
