# 🚀 EKS Cloud-Native CI/CD Pipeline: Pac-Man Microservices

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
