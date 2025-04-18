# Jenkins-CICD
This project demonstrates a complete CI/CD pipeline using Jenkins, Docker, and Kubernetes. It automates the process of building, testing, and deploying a Python Flask application, ensuring efficient and reliable software delivery.​

## Overview

The Jenkins-CICD project encapsulates the following DevOps practices:​

Continuous Integration: Automated testing and building of the application upon code commits.

Continuous Deployment: Seamless deployment of the Dockerized application to a Kubernetes cluster.

_____________________________________________________________________________________________________________________
## Prerequisites

Docker

Kubernetes (e.g., Minikube or a cloud provider)

Jenkins

Kubectl

Python 3.x​

aws account
_____________________________________________________________________________________________________
## Project Structure
```
├── app.py                 # Main Flask application
├── requirements.txt       # Python dependencies
├── Dockerfile             # Docker image definition
├── Jenkinsfile            # Jenkins pipeline configuration
├── kubernetsk8/           # Kubernetes deployment manifests
│   ├── deployment.yaml
│   └── service.yaml
└── templates/             # HTML templates for Flask
    └── index.html
```
_______________________________________________________________________________________________________

## Step 1: Run the Flask app locally

1. Clone the git repo:
   ```
   git clone https://github.com/ChethanRaj13/Jenkins-CICD.git
   cd Jenkins-CICD
   ```
2. Build the Docker Image
   ```
   docker build -t flask-app.
   ```
3. Run the Application Locally
   ```
   docker run -d -p 5000:5000 flask-app
   ```
Access the application at http://localhost:5000

________________________________________________________________________________________________________

## Step 2: Creating Jenkins pipeline
This section explains how to set up and run the Jenkins pipeline on an AWS EC2 instance to build, test, and deploy your Dockerized Flask app using Jenkins

### Launch an EC2 Instance
   
Use Ubuntu 22.04 LTS or similar.

Instance type: t2.medium or higher (to handle Docker builds).

Enable HTTP, HTTPS, and port 8080 in the security group for Jenkins access.

### Install Required Tools
login to your EC2 instance through SSH and install:
```
# Update packages
sudo apt update && sudo apt upgrade -y

# Install Docker
sudo apt install docker.io -y
sudo systemctl enable docker
sudo usermod -aG docker jenkins
sudo systemctl restart jenkins

# Install Jenkins
curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo tee \
  /usr/share/keyrings/jenkins-keyring.asc > /dev/null

echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null

sudo apt update
sudo apt install openjdk-17-jdk jenkins -y
sudo systemctl enable jenkins
sudo systemctl start jenkins
Access Jenkins: http://<your-ec2-public-ip>:8080
```

### Access Jenkins
1. Open your browser and go to:
```
http://<your-ec2-public-ip>:8080
```
(Replace <your-ec2-public-ip> with your actual AWS EC2 instance's public IP address.)

2. Get the Administrator Password
On your EC2 terminal, run the following command to fetch the initial admin password:
```
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```
You'll see a long alphanumeric string like: e7f8c56d4d514b349fafd24bd8bc5a5e

Copy this and paste it into the “Administrator password” field on the Jenkins setup page.

3. Install Suggested Plugins
After logging in, Jenkins will prompt you to:

Install suggested plugins (recommended)

4. Create Your Admin User
After plugin installation, Jenkins will ask you to create a user.

Set your username, password, full name, and email.

This will be your Jenkins admin account going forward.

3. Install Required Jenkins Plugins
Inside Jenkins UI:

Go to Manage Jenkins > Plugins

Install:

Docker Pipeline

Sonarqube scanner

4. Configure Jenkins Credentials
Go to Manage Jenkins > Credentials > Global

Add Username & Password with your Docker Hub credentials.

ID must be: docker-hub-creds (as used in the Jenkinsfile).

Add a secret text with your generated GitHub repo token.

5. Set Up Your Jenkins Job
Create a New Item → choose Pipeline → name it (e.g., Jenkins-CICD).

Under Pipeline > Definition, choose Pipeline script from SCM:

SCM: Git

Repo URL: https://github.com/ChethanRaj13/Jenkins-CICD.git

Script Path: Jenkinsfile

Restart Jenkins after the setup to apply all user group changes.
```
http://<your-ec2-public-ip>:8080/restart
```

____________________________________________________________________________________________________
## Step 3: Doing CD using ArgoCD

1. Install Argo CD on a Kubernetes Cluster
Use an existing Kubernetes cluster like:

Minikube (for local testing)

EKS (on AWS)

If you're using Minikube:
```
minikube start
```
Run the following to install Argo CD:
```
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

2. Expose Argo CD UI
Argo CD UI runs inside the argocd-server pod.
```
kubectl port-forward svc/argocd-server -n argocd 8080:443
```
Now access the UI at: https://localhost:8080

3. Log in to Argo CD UI
Default Username:
```
admin
```
Get Initial Password:
```
kubectl -n argocd get secret argocd-initial-admin-secret \
  -o jsonpath="{.data.password}" | base64 -d && echo
```
Now log in to the UI at https://local:8080 using the above credentials.

4. Connect to Your Git Repo
You need a Git repo with Kubernetes manifests for your Flask app.

Example structure:
```
flask-app-k8s/
├── deployment.yaml
├── service.yaml
└── kustomization.yaml (optional)
```
Ensure the Docker image path in your deployment.yaml matches what's pushed by your Jenkins pipeline:

image: chethanraj13/jenkins:<tag>

5. Create a New Argo CD App via UI
Go to Applications → + NEW APP

Fill in the fields:

App Name: flask-app

Project: default

Sync Policy: Automatic

Repository URL:	https://github.com/YourUsername/RepoName.git

Revision: HEAD 

Path: kubernetsk8

Cluster: https://kubernetes.default.svc

Namespace	default (or your choice)

Click Create

6. Sync and Deploy
Select your app from the dashboard

Click SYNC → Synchronize

Argo CD will deploy the app using the Kubernetes manifests from your Git repo
