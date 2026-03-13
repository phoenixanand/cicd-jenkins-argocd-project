🚀 End-to-End CI/CD Pipeline with Jenkins, Docker, Kubernetes, ArgoCD & AWS (GitOps)

A complete production-style CI/CD implementation that automates everything from code commit to Kubernetes deployment using GitOps principles.

Project Overview

This project demonstrates how to build a fully automated CI/CD pipeline for a Java application using:
 
 1) Jenkins – Continuous Integration
 2) Maven – Build & Dependency Management
 3) SonarQube – Static Code Analysis
 4) Docker – Containerization
 5) DockerHub / ECR – Image Registry
 6) Kubernetes – Container Orchestration
 7) ArgoCD – GitOps Continuous Deployment
 8) Minikube – Local Kubernetes Cluster
 9) AWS EC2 – Cloud Infrastructure

<img width="1296" height="563" alt="image" src="https://github.com/user-attachments/assets/fd1864c3-3c37-48eb-acbd-a87ef33dfd98" />

1️) AWS EC2 Setup (CI Server)

Launch EC2 instance (t2.large recommended)
Open inbound ports:
                  customTCP - 8080 (Jenkins)
                  customTCP - 9000 (SonarQube)
Install Dependencies
sudo apt update -y
sudo apt install openjdk-17-jdk -y

2) Now install the docker

sudo apt install -y apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository \
"deb [arch=amd64] https://download.docker.com/linux/ubuntu \
$(lsb_release -cs) stable"
sudo apt update
sudo apt install -y docker-ce
sudo systemctl start docker
sudo systemctl enable docker
sudo usermod -aG docker $USER

3) Install Jenkins

Install Jenkins
Install required plugins:
                       Docker Pipeline
                       SonarQube Scanner
                       Git
                       Pipeline

Access:
http://<EC2_PUBLIC_IP>:8080

4) setup SonarQube (Dockerized)
docker network create cicd-network
Run PostgreSQL:

docker run -d \
  --name sonar-postgres \
  --network cicd-network \
  -e POSTGRES_USER=sonar \
  -e POSTGRES_PASSWORD=sonar \
  -e POSTGRES_DB=sonar \
  postgres:15

Run SonarQube:

docker run -d \
  --name sonarqube \
  --network cicd-network \
  -p 9000:9000 \
  -e SONAR_JDBC_URL=jdbc:postgresql://sonar-postgres:5432/sonar \
  -e SONAR_JDBC_USERNAME=sonar \
  -e SONAR_JDBC_PASSWORD=sonar \
  sonarqube:lts-community

Access:

http://<EC2_PUBLIC_IP>:9000

<img width="1880" height="1078" alt="image" src="https://github.com/user-attachments/assets/870fd4cc-585f-4b9d-b130-c4f3608b1bab" />

5️) Jenkins Credentials Setup

Add:

DockerHub credentials
GitHub Personal Access Token
SonarQube token

Path:
Manage Jenkins → Credentials → System → Global


6️) Jenkins Pipeline (CI)

Pipeline stages:

Checkout
Build & Test (Maven)
Static Code Analysis (SonarQube)
Docker Build
Docker Push

<img width="1918" height="1078" alt="image" src="https://github.com/user-attachments/assets/539c34e4-a792-486e-8dad-51d982e1e723" />

Example command:

mvn clean package
docker build -t <username>/<app>:<tag> .
docker push <username>/<app>:<tag>
Kubernetes & CD Setup

7️) Start Minikube
minikube start --driver=docker

8️) Install ArgoCD
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

Expose ArgoCD:
kubectl port-forward svc/argocd-server -n argocd 8000:443

Access:
https://localhost:8000


<img width="1881" height="972" alt="image" src="https://github.com/user-attachments/assets/17c86cfd-7783-4434-9286-af0092e30966" />




