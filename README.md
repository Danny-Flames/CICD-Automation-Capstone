# Automating Deployment of an E-Commerce Website with Jenkins

## Project Overview

This project focuses on implementing a **CI/CD pipeline** using **Jenkins** to automate the deployment of an e-commerce web application. The goal is to ensure **continuous integration, continuous deployment**, and maintain the **scalability and reliability** of the application.

## Prerequisites

Before starting, ensure you have:

- Basic knowledge of **Jenkins**.
- Completed **Introduction to Jenkins, Jenkins Freestyle Projects, and Jenkins Pipeline Jobs**.
- A **dedicated Jenkins server** set up.
- **GitHub/Bitbucket repository** for source code management.
- **Docker** installed for containerization.
- A **container registry** (e.g., Docker Hub) for storing Docker images.

---

## CI/CD Pipeline Implementation

### 1. Jenkins Server Setup

#### Objective:
Configure Jenkins to automate the CI/CD pipeline.

#### Steps:
- (a). **Install Jenkins** on a dedicated server:
   ```sh
   sudo apt update && sudo apt install -y openjdk-11-jdk
   wget -q -O - https://pkg.jenkins.io/debian/jenkins.io.key | sudo apt-key add -
   echo "deb http://pkg.jenkins.io/debian-stable binary/" | sudo tee /etc/apt/sources.list.d/jenkins.list
   sudo apt update
   sudo apt install -y jenkins
   ```

- (b). **Start and enable Jenkins**:
    ```sh
    sudo systemctl start jenkins
    sudo systemctl enable jenkins
    ```

- (c). **Install required plugins**:
    - Navigate to Manage Jenkins > Manage Plugins.
    - Install: Git, Docker, Pipeline, Blue Ocean, SSH Pipeline Steps.

- (d). **Configure security**:
    - Enable authentication (Manage Jenkins > Configure Global Security).
    - Restrict anonymous access.


### 2. Source Code Management Integration

#### Objective:
Integrate Jenkins with a version control system for automatic build triggers.

#### Steps:
- (a) Connect Jenkins to GitHub:
    - Install the Git plugin in Jenkins.
    - Add GitHub credentials (Manage Jenkins > Manage Credentials).
    - Configure repository URL in Jenkins jobs.

- (b) Enable Webhooks for automatic builds:
    - In GitHub, go to Settings > Webhooks.
    - Add a new webhook:
        - Payload URL: http://your-jenkins-server/github-webhook/


### 3. Jenkins Freestyle Jobs for Build and Unit Tests

#### Objective:
Set up Jenkins Freestyle jobs to build the application and run tests.

#### Steps:
- Create a new Freestyle Job in Jenkins.
- Connect to GitHub repository (Source Code Management > Git).
- Configure build steps:
    - Use a shell script to build the application:
    ```sh
    npm install
    npm run build
    ```
- Save and trigger builds on code changes.


### 4. Jenkins Pipeline for Web Application

#### Objective:
Develop a Jenkins Pipeline to automate deployment.

#### Steps:
- Create a Jenkinsfile in your repository:

    ```sh
        pipeline {
        agent any

        stages {
            stage('Clone Repository') {
                steps {
                    git 'https://github.com/your-repo.git'
                }
            }
            stage('Build') {
                steps {
                    sh 'npm install'
                    sh 'npm run build'
                }
            }
            stage('Test') {
                steps {
                    sh 'npm test'
                }
            }
            stage('Docker Build & Push') {
                steps {
                    sh 'docker build -t your-dockerhub-username/app:latest .'
                    sh 'docker push your-dockerhub-username/app:latest'
                }
            }
            stage('Deploy') {
                steps {
                    sh 'docker run -d -p 80:80 your-dockerhub-username/app:latest'
                }
            }
        }
    }
    ```

- Create a new Pipeline Job in Jenkins.
- Point to the Jenkinsfile in your repository.
- Save and run the pipeline.


### 5. Docker Image Creation and Registry Push

#### Objective:
Automate Docker image creation and push it to a registry.

#### Steps:
- Enable Docker in Jenkins:
    ```sh
        sudo usermod -aG docker jenkins
        sudo systemctl restart jenkins
    ```

- Login to Docker Hub:
    ```sh
        docker login -u your-dockerhub-username -p your-password
    ```

- Modify Jenkinsfile to include:
    ```sh
        docker build -t your-dockerhub-username/app:latest .
        docker push your-dockerhub-username/app:latest
    ```


### 6. Running the Web Application in a Container

#### Objective:
Deploy the web application using Docker.

#### Steps:
- Run the container:
    ```sh
        docker run -d -p 8080:80 your-dockerhub-username/app:latest
    ```

- Access the application in your browser:
    ```sh
        http://localhost:8080
    ```


### Security Best Practices
- Use Token or SSH keys for authentication instead of passwords.
- Limit Jenkins user permissions using Role-Based Access Control (RBAC).
- Enable HTTPS on Jenkins for secure communication.
- Regularly update Jenkins and plugins to patch vulnerabilities.
- Scan Docker images for security issues before deployment.


## Conclusion
This project demonstrates how to automate deployment using Jenkins, Git, Docker, and a CI/CD pipeline. The setup ensures continuous integration, efficient testing, and seamless deployment of the web application.

## Author
- Name: Ekpa Daniel Egwoke
- Email: daniel.ekpa@yahoo.com
- LinkedIn: https://www.linkedin.com/in/ekpa-daniel/