# DevFlow 

## Overview

**DevFlow** is a simple end-to-end DevOps CI/CD pipeline project that demonstrates how code pushed to GitHub can automatically build, package, and deploy an application using modern DevOps tools.

The pipeline uses:

* **GitHub** – Source code repository
* **Jenkins** – CI/CD automation server
* **Docker** – Containerization of the application
* **Docker Hub** – Container image registry
* **Kubernetes (Minikube)** – Container orchestration and deployment

Whenever a developer pushes code to GitHub, Jenkins automatically triggers a pipeline that:

1. Builds a Docker image
2. Pushes the image to Docker Hub
3. Deploys the application to a Kubernetes cluster

---

# Architecture

```
Developer
   │
   │ Git Push
   ▼
GitHub Repository
   │
   │ SCM Trigger
   ▼
Jenkins Pipeline
   │
   │ Build Docker Image
   ▼
Docker
   │
   │ Push Image
   ▼
Docker Hub
   │
   │ Pull Image
   ▼
Kubernetes (Minikube)
   │
   ▼
Running Application
```

---

# Project Structure

```
devflow/
│
├── app.py
├── requirements.txt
├── Dockerfile
├── Jenkinsfile
│
└── k8s/
    ├── deployment.yaml
    └── service.yaml
```

---

# Application

A simple **Python Flask web application** used to demonstrate CI/CD deployment.

## app.py

```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def home():
    return "Hello from DevFlow CI/CD Pipeline!"

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)
```

---

# Docker Setup

The application is containerized using Docker.

## Dockerfile

```dockerfile
FROM python:3.11-slim

WORKDIR /app

COPY requirements.txt .

RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 5000

CMD ["python", "app.py"]
```

---

# CI/CD Pipeline (Jenkins)

The Jenkins pipeline performs the following stages:

1. Build Docker Image
2. Login to Docker Hub
3. Push Image to Docker Hub
4. Deploy to Kubernetes

## Jenkinsfile

```groovy
pipeline {
    agent any

    environment {
        IMAGE_NAME = 'YOUR_DOCKERHUB_USERNAME/simple-devops-app'
        IMAGE_TAG = 'latest'
        KUBECONFIG = 'C:\\Users\\ADMIN\\.kube\\config'
    }

    stages {

        stage('Build Docker Image') {
            steps {
                bat "docker build -t %IMAGE_NAME%:%IMAGE_TAG% ."
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    bat 'echo %DOCKER_PASS% | docker login -u %DOCKER_USER% --password-stdin'
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                bat "docker push %IMAGE_NAME%:%IMAGE_TAG%"
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                bat 'kubectl apply -f k8s/deployment.yaml'
                bat 'kubectl apply -f k8s/service.yaml'
            }
        }

    }

    post {
        always {
            echo 'Pipeline finished'
        }
    }
}
```

---

# Kubernetes Deployment

## deployment.yaml

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: simple-devops-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: simple-devops-app
  template:
    metadata:
      labels:
        app: simple-devops-app
    spec:
      containers:
        - name: simple-devops-app
          image: YOUR_DOCKERHUB_USERNAME/simple-devops-app:latest
          ports:
            - containerPort: 5000
          imagePullPolicy: Always
```

---

## service.yaml

```yaml
apiVersion: v1
kind: Service
metadata:
  name: simple-devops-service
spec:
  type: NodePort
  selector:
    app: simple-devops-app
  ports:
    - protocol: TCP
      port: 5000
      targetPort: 5000
      nodePort: 30008
```

---

# How the Pipeline Works

1. Developer pushes code to **GitHub**
2. Jenkins detects the change through SCM polling
3. Jenkins pipeline starts automatically
4. Docker builds the application image
5. Jenkins pushes the image to **Docker Hub**
6. Kubernetes pulls the image
7. The application is deployed to **Minikube**

---

# Running the Application

Check Kubernetes resources:

```
kubectl get pods
kubectl get services
```

Open the application:

```
minikube service simple-devops-service
```

---

# Tools Used

* GitHub
* Jenkins
* Docker
* Docker Hub
* Kubernetes
* Minikube
* Python (Flask)

---

# Future Improvements

Possible improvements to this project:

* Use dynamic Docker image tags (Git commit SHA)
* Add automated testing stage
* Integrate Slack notifications
* Implement Blue-Green or Canary deployments
* Use Helm for Kubernetes deployments

---

# Author

Gayathri J

This project demonstrates a complete beginner-friendly **DevOps CI/CD pipeline implementation** using Jenkins, Docker, and Kubernetes.
