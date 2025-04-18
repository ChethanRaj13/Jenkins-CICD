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
