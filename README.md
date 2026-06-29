# 🚀 EKS Cloud-Native CI/CD Pipeline: Pac-Man Microservices

## 📑 Table of Contents
* [Overview](#-overview)
* [Cloud Architecture](#-cloud-architecture)
* [Tech Stack](#-tech-stack)
* [CI/CD Pipeline Workflow](#-cicd-pipeline-workflow)
* [Repository Structure](#-repository-structure)
* [How to Trigger the Deployment](#-how-to-trigger-the-deployment)
---

## 📌 Overview
This repository demonstrates a complete Cloud-Native deployment of a multi-tier web application (Pac-Man) on Amazon EKS. It features a fully automated CI/CD pipeline, passwordless AWS authentication via OIDC, and infrastructure provisioning using `eksctl` with Auto Mode.

## 🏗️ Cloud Architecture

```mermaid
graph TD
    Developer([Developer]) -->|git push| GitHub[GitHub Actions CI/CD]
    
    subgraph AWS Cloud
        GitHub -->|OIDC Auth + Push Image| ECR[(Amazon ECR)]
        
        subgraph EKS Cluster
            GitHub -.->|kubectl set image| EKS_API
            NLB[Network Load Balancer] -->|Port 80 to 8080| Pacman(Pac-Man Deployment<br/>3 Replicas)
           Pacman -->|Port 27017| Mongo[(MongoDB StatefulSet<br/>2 Replicas)]
            Mongo --- EBS[EBS gp3 Storage]
        end
        
        ECR -.->|Image Pull| Pacman
    end
    
    User([Gamer / User]) -->|HTTP| NLB
```

## 🛠️ Tech Stack

* **Cloud Provider:** AWS (EKS, ECR, IAM, NLB, EBS)
* **Containerization:** Docker
* **Orchestration:** Kubernetes (Deployments, StatefulSets, Services, StorageClasses)
* **CI/CD:** GitHub Actions (OIDC Integration)
* **Database:** MongoDB

## 🔄 CI/CD Pipeline Workflow

* **Push:** Developer pushes code changes to the master branch.
* **Auth:** GitHub Actions authenticates securely with AWS using a short-lived OIDC token (eliminating the need for static access keys).
* **Build & Push:** A new Docker image is built, tagged with the unique Git Commit SHA ($GITHUB_SHA), and pushed to Amazon ECR.
* **Deploy:** The pipeline updates the EKS cluster using an imperative kubectl set image command, rolling out the new version dynamically with zero downtime.

## 📂 Repository Structure

* **.github/workflows/main_secure.yml:** The CI/CD pipeline definition.
* **ekscluster/cluster.yaml:** Infrastructure as Code (IaC) for EKS Auto Mode.
* **k8s/:** Kubernetes manifests (Deployment, StatefulSet, Services, gp3 StorageClass).

## ⚡ How to Trigger the Deployment

This project embraces true Continuous Deployment. To trigger the entire infrastructure update, build, and deployment process, simply commit and push your code to the master branch:

```bash
git commit -am "Update code"
git push origin master
```
<img width="881" height="691" alt="diagram_pacman drawio" src="https://github.com/user-attachments/assets/a84f9645-5b14-4259-815d-7408c6cd124e" />
<img width="1600" height="729" alt="workflow" src="https://github.com/user-attachments/assets/26f05d8e-41eb-40e8-b3ca-699878431376" />
<img width="1336" height="912" alt="pacman" src="https://github.com/user-attachments/assets/962573d6-bcc0-47dc-9134-10adc4729cb8" />



