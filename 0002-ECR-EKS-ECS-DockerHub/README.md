# ☁️ Vyomanant Academy — AWS Container Deployment & CI/CD Lab Roadmap

Final learning path: ECR → EKS → ECR + Docker Hub + EKS + GitHub Actions

We will follow a Console-first, CLI-when-required approach. EKS cluster creation and deletion will use reusable shell scripts, while application deployment and troubleshooting will use essential Docker, kubectl, and AWS CLI commands.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🎯 Lab Execution Standards

| Activity                                 | Preferred Method            |
| ---------------------------------------- | --------------------------- |
| AWS IAM, roles, permissions              | AWS Console                 |
| ECR repository creation and settings     | AWS Console                 |
| Docker image build, tag, push, pull      | Docker CLI                  |
| EKS cluster creation                     | Shell script using `eksctl` |
| EKS cluster deletion                     | Shell script using `eksctl` |
| EKS cluster inspection                   | AWS Console + kubectl       |
| Kubernetes application deployment        | YAML + kubectl              |
| Kubernetes Services and scaling          | YAML + kubectl              |
| AWS Load Balancer inspection             | AWS Console                 |
| GitHub repositories and secrets/settings | GitHub UI                   |
| GitHub Actions workflows                 | YAML                        |
| CI/CD authentication to AWS              | GitHub OIDC + IAM role      |
| Verification and troubleshooting         | AWS Console + basic CLI     |

Important: We will not use AWS CLI for tasks that are easier to teach using the AWS Console. However, Docker CLI, kubectl, and eksctl remain essential hands-on tools.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 📦 Module 14 — Amazon ECR

5 Essential Labs

Goal: Learn to store, manage, and distribute Docker images using Amazon ECR.

| Lab ID | Lab Title                                             | Method               |
| ------ | ----------------------------------------------------- | -------------------- |
| 14001  | AWS ECR Fundamentals, IAM Permissions & Prerequisites | Console              |
| 14002  | Create and Configure a Private ECR Repository         | Console              |
| 14003  | Build, Tag, Push & Pull Docker Images Using ECR       | Console + Docker CLI |
| 14004  | Manage ECR Image Tags, Versions & Lifecycle Policies  | Console              |
| 14005  | ECR Image Scanning, Repository Security & Cleanup     | Console              |

Outcome: Students understand ECR repositories and can push and pull Docker images securely.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# ☸️ Module 15 — Amazon EKS

9 Essential Labs

Goal: Create and manage Kubernetes clusters on AWS using scripts, the AWS Console, and kubectl.

| Lab ID | Lab Title                                                       | Method            |
| ------ | --------------------------------------------------------------- | ----------------- |
| 15001  | EKS Architecture, IAM, VPC & Cluster Prerequisites              | Console           |
| 15002  | Install and Configure AWS CLI, kubectl & eksctl                 | CLI               |
| 15003  | Create an EKS Cluster Using a Reusable Shell Script             | Shell Script      |
| 15004  | Explore EKS Cluster, Managed Nodes, Pods & Namespaces           | Console + kubectl |
| 15005  | Deploy Your First NGINX Application on EKS                      | YAML + kubectl    |
| 15006  | Expose EKS Applications Using ClusterIP & LoadBalancer Services | YAML + Console    |
| 15007  | Perform Rolling Updates, Rollbacks & Application Scaling        | kubectl           |
| 15008  | Configure EKS Access, Kubernetes RBAC & Basic Troubleshooting   | Console + kubectl |
| 15009  | Delete an EKS Cluster Using a Reusable Shell Script             | Shell Script      |

Outcome: Students can create and delete EKS clusters using scripts and perform essential Kubernetes operations.

### Cluster lifecycle approach

We will provide two reusable scripts:

```
eks-cluster-create.sh
eks-cluster-delete.sh
```

The creation script will use a documented, consistent EKS configuration with managed node groups. The deletion script will target only the lab cluster and include appropriate safety checks.

Cost management: Students will reuse the cluster throughout Module 15 and Module 16. Lab 15009 teaches deletion, but the actual final cleanup should happen after completing Module 16.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🚀 Module 16 — ECR + Docker Hub + EKS + GitHub Actions

Primary Integration Module — 10 Labs

Goal: Teach manual container deployment first, then automate builds, image publishing, and Kubernetes deployment using GitHub Actions.

| Lab ID | Lab Title                                                                    | Method                  |
| ------ | ---------------------------------------------------------------------------- | ----------------------- |
| 16001  | Understand Docker Hub, ECR, EKS & GitHub Actions Integration Architecture    | Architecture + Console  |
| 16002  | Build a Custom Docker Image & Push to Docker Hub                             | Docker CLI              |
| 16003  | Deploy a Docker Hub Image to AWS EKS                                         | YAML + kubectl          |
| 16004  | Push the Same Docker Image to AWS ECR                                        | Console + Docker CLI    |
| 16005  | Deploy a Private ECR Image to EKS & Expose the Application                   | YAML + kubectl          |
| 16006  | Configure GitHub Repository, GitHub Actions & AWS OIDC Authentication        | GitHub UI + AWS Console |
| 16007  | Automate Docker Image Build & Push to Docker Hub Using GitHub Actions        | Workflow YAML           |
| 16008  | Automate Docker Image Build & Push to AWS ECR Using GitHub Actions           | Workflow YAML           |
| 16009  | Automate EKS Deployment and Rolling Updates Using GitHub Actions             | Workflow YAML           |
| 16010  | Capstone — Complete GitHub Actions CI/CD Pipeline with Docker Hub, ECR & EKS | End-to-End Project      |

Outcome: Students can implement both Docker Hub-based and ECR-based image delivery, then automate EKS deployment with GitHub Actions.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🏗️ Final CI/CD Architecture

```
                 ┌────────────────────┐
                 │     Developer      │
                 │  Application Code  │
                 └─────────┬──────────┘
                           │ Git Push
                           ▼
                 ┌────────────────────┐
                 │ GitHub Repository  │
                 └─────────┬──────────┘
                           │ Trigger
                           ▼
                 ┌────────────────────┐
                 │   GitHub Actions   │
                 │    Docker Build    │
                 └─────────┬──────────┘
                           │
                ┌──────────┴──────────┐
                │                     │
                ▼                     ▼
     ┌──────────────────┐  ┌──────────────────┐
     │    Docker Hub    │  │     AWS ECR      │
     │  Image Registry  │  │ Private Registry │
     └─────────┬────────┘  └────────┬─────────┘
               │                    │
               │    Image Pull      │
               │                    │
               └──────────┬─────────┘
                          ▼
              ┌───────────────────────┐
              │        AWS EKS        │
              │                       │
              │ Kubernetes Deployment │
              │         Pods          │
              │        Service        │
              └───────────┬───────────┘
                          │
                          ▼
              ┌───────────────────────┐
              │   AWS Load Balancer   │
              └───────────┬───────────┘
                          │
                          ▼
                       End Users
```

Architecture note: Docker Hub and ECR are alternative image sources for Kubernetes workloads. GitHub Actions can publish to both registries, but each Kubernetes Deployment references its configured image. The final capstone will demonstrate this clearly.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 📊 Final Lab Distribution

| Module | Focus                                   | Labs |
| ------ | --------------------------------------- | ---- |
| 14     | Amazon ECR                              | 5    |
| 15     | Amazon EKS                              | 9    |
| 16     | ECR + Docker Hub + EKS + GitHub Actions | 10   |
| Total  | Core Training                           | 24   |

This is the recommended finalized roadmap: 24 focused, practical labs, without a separate ECS module.

For every lab, we will maintain the Vyomanant Academy GitHub README format, with Console navigation, minimal essential commands, reusable scripts where needed, verification, troubleshooting, and safe cleanup.
