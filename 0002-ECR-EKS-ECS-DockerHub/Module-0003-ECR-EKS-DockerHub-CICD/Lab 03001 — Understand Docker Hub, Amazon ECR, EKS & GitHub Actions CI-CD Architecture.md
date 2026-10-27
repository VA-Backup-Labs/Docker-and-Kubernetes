# 🧪 Lab 03001 — Understand Docker Hub, Amazon ECR, EKS & GitHub Actions CI/CD Architecture

Explore how Docker Hub, Amazon ECR, Amazon EKS, and GitHub Actions work together to build, store, deploy, and update containerized applications.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🌍 Lab Overview

In previous modules, we learned how to manage Docker images using Amazon ECR and deploy containerized applications using Amazon EKS.

In Module 03, we will integrate these technologies with GitHub Actions to build a complete container CI/CD workflow.

This introductory lab focuses on understanding the architecture and preparing the required accounts and existing infrastructure.

| Property          | Details                                                    |
| ----------------- | ---------------------------------------------------------- |
| Lab ID            | 03001                                                      |
| Module            | 03 — Container CI/CD Integration                           |
| Difficulty        | Beginner                                                   |
| Duration          | 20–25 minutes                                              |
| Tools             | Docker, Docker Hub, Amazon ECR, Amazon EKS, GitHub Actions |
| Environment       | Ubuntu Linux + AWS + GitHub                                |
| Approach          | Architecture + Console Exploration                         |
| Resources Created | None                                                       |

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🎯 Learning Objectives

By the end of this lab, you will be able to:

1. Explain the purpose of Docker Hub and Amazon ECR.
2. Understand how EKS pulls container images.
3. Identify the role of GitHub Actions in CI/CD.
4. Describe a container build-and-deploy workflow.
5. Understand GitHub Actions authentication with AWS.
6. Identify the resources required for integration.
7. Differentiate manual deployment from automated deployment.
8. Prepare the environment for upcoming practical labs.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧰 Prerequisites

Ensure you have:

- Completed Module 01 — Amazon ECR.
- Completed Module 02 — Amazon EKS.
- An active AWS account.
- An existing ECR private repository named `myapp`.
- An active EKS cluster named `vyomanant-eks`.
- An existing Kubernetes namespace named `vyomanant-lab`.
- Docker installed on Ubuntu Linux.
- AWS CLI, kubectl, and eksctl installed.
- A GitHub account.
- A Docker Hub account.

### 🔎 Verify the Existing Environment

```
docker --version
```

```
aws --version
```

```
kubectl version --client
```

```
export AWS_PROFILE=vyomanant
```

```
aws sts get-caller-identity
```

```
kubectl get nodes
```

✅ Expected: Docker, AWS CLI, and kubectl are available, AWS authentication works, and the EKS worker nodes are `Ready`.

⚠️ Cost Reminder: Your existing EKS cluster and worker nodes continue generating AWS charges while running.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🏗️ Lab Architecture

The following diagram represents the target architecture for Module 03. It is not fully deployed in this lab.

```
                    Developer
                        │
                        │ git push
                        ▼
              ┌──────────────────┐
              │ GitHub Repository│
              │ Application Code │
              │ Dockerfile       │
              └────────┬─────────┘
                       │
                       ▼
              ┌──────────────────┐
              │ GitHub Actions   │
              │ CI/CD Workflow   │
              └────────┬─────────┘
                       │
                       │ Docker Build
                       ▼
              ┌──────────────────┐
              │ Container Image  │
              └────────┬─────────┘
                       │
              ┌────────┴─────────┐
              │                  │
              ▼                  ▼
     ┌────────────────┐  ┌────────────────┐
     │ Docker Hub     │  │ Amazon ECR     │
     │ Image Registry │  │ Private Registry│
     └────────┬───────┘  └────────┬───────┘
              │                   │
              │ Image Pull        │ Image Pull
              │                   │
              └─────────┬─────────┘
                        ▼
              ┌──────────────────┐
              │ Amazon EKS       │
              │ vyomanant-eks    │
              │                  │
              │ Kubernetes       │
              │ Deployments      │
              │ Pods             │
              │ Services         │
              └──────────────────┘
```

Architecture explanation:

- GitHub stores application code and CI/CD workflows.
- GitHub Actions builds container images and automates deployment.
- Docker Hub stores images in a public or private Docker registry.
- Amazon ECR stores container images in AWS.
- Amazon EKS runs applications using images from these registries.

Important: The two image registries are alternative image sources for EKS. A single Deployment normally references one image registry at a time.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🚀 Step-by-Step Instructions

## 🔹 Step 1 — Understand the Container CI/CD Workflow

🎯 Objective: Identify the main stages of container delivery.

A typical workflow follows this sequence:

```
Developer
    │
    ▼
Write Application Code
    │
    ▼
Create Dockerfile
    │
    ▼
Build Docker Image
    │
    ▼
Push Image to Registry
    │
    ▼
Deploy Image to EKS
    │
    ▼
Verify Application
```

When GitHub Actions is introduced, these operations can run automatically after a code change.

### CI vs CD

| Concept                     | Purpose                                                                       |
| --------------------------- | ----------------------------------------------------------------------------- |
| Continuous Integration (CI) | Automatically builds and validates application changes                        |
| Continuous Delivery (CD)    | Prepares validated application changes for deployment                         |
| Continuous Deployment       | Automatically deploys approved changes without a separate manual release step |

🧠 Why It Matters: CI/CD reduces repetitive manual work and helps standardize application delivery.

## 🔹 Step 2 — Explore Docker Hub

🎯 Objective: Understand the role of Docker Hub in image distribution.

1. Open [Docker Hub](https://hub.docker.com/?utm_source=chatgpt.com).
2. Sign in to your account.
3. Open My Hub → Repositories.
4. Review any existing repositories.
5. Identify your Docker Hub username.

### Docker Hub Image Format

```
dockerhub-username/repository-name:tag
```

Example:

```
yourusername/myapp:v1
```

| Component      | Meaning              |
| -------------- | -------------------- |
| `yourusername` | Docker Hub namespace |
| `myapp`        | Repository name      |
| `v1`           | Image version tag    |

Important: Replace `yourusername` with your actual Docker Hub username in upcoming labs.

✅ Expected: You can access Docker Hub and identify your username.

## 🔹 Step 3 — Explore the Existing Amazon ECR Repository

🎯 Objective: Review the private image registry created in Module 01.

1. Open the [AWS Management Console](https://console.aws.amazon.com/?utm_source=chatgpt.com).
2. Navigate to Amazon ECR.
3. Select Private registry → Repositories.
4. Open `myapp`.
5. Review its images and repository URI.

### Amazon ECR Image Format

```
123456789012.dkr.ecr.ap-south-1.amazonaws.com/myapp:v1
```

| Component      | Meaning                |
| -------------- | ---------------------- |
| `123456789012` | Example AWS account ID |
| `ap-south-1`   | AWS Region             |
| `myapp`        | ECR repository         |
| `v1`           | Image tag              |

Important: Use your actual AWS account ID and repository URI.

✅ Expected: The private ECR repository `myapp` is accessible.

## 🔹 Step 4 — Explore the Existing Amazon EKS Cluster

🎯 Objective: Verify the Kubernetes environment used for deployments.

1. Open AWS Console → Amazon EKS.
2. Select Clusters.
3. Open `vyomanant-eks`.
4. Verify the cluster status is Active.
5. Open Compute and review the `workers` node group.

Check the Kubernetes namespace:

```
kubectl get namespaces
```

Check existing workloads:

```
kubectl get deployments -n vyomanant-lab
```

✅ Expected: The `vyomanant-lab` namespace and existing NGINX Deployment are available.

🧠 Why It Matters: We will reuse this cluster rather than creating another billable EKS environment.

## 🔹 Step 5 — Understand Docker Hub vs Amazon ECR

🎯 Objective: Compare the two container registries.

| Feature         | Docker Hub                          | Amazon ECR                       |
| --------------- | ----------------------------------- | -------------------------------- |
| Provider        | Docker                              | AWS                              |
| Image Storage   | Public or private repositories      | Public or private repositories   |
| Authentication  | Docker Hub credentials or tokens    | AWS IAM-based authorization      |
| EKS Integration | Pulls supported images              | Integrates with AWS IAM          |
| Typical Use     | Public images, development, sharing | AWS-based application delivery   |
| Pricing         | Depends on Docker plan and usage    | Depends on AWS usage and pricing |

### Image Pulling on EKS

For public Docker Hub images, EKS worker nodes can normally pull images without registry credentials, subject to Docker Hub pull limits.

For private ECR images in the same AWS account, EKS managed EC2 worker nodes can pull images when their node IAM role has the required ECR permissions.

Important: Private Docker Hub images require additional registry authentication, typically through a Kubernetes image pull Secret.

## 🔹 Step 6 — Explore GitHub Actions

🎯 Objective: Understand how GitHub Actions automates container delivery.

1. Sign in to [GitHub](https://github.com/?utm_source=chatgpt.com).
2. Open an existing repository or explore the repository creation page.
3. Locate the Actions tab.
4. Review available workflow templates.

GitHub Actions workflows are defined using YAML files.

### Standard Workflow Location

```
.github/
└── workflows/
    └── deploy.yml
```

### Important GitHub Actions Concepts

| Component | Purpose                          |
| --------- | -------------------------------- |
| Workflow  | Defines an automated process     |
| Event     | Triggers a workflow              |
| Job       | Groups execution steps           |
| Runner    | Executes workflow jobs           |
| Step      | Performs an individual task      |
| Action    | Reusable automation component    |
| Secrets   | Stores sensitive workflow values |

Note: We will create the actual GitHub repository and workflows in upcoming labs.

✅ Expected: You can locate the GitHub Actions interface.

## 🔹 Step 7 — Understand GitHub Actions Authentication with AWS

🎯 Objective: Understand how GitHub Actions can securely access AWS.

For this training, we will use OpenID Connect (OIDC) rather than storing permanent AWS access keys in GitHub.

### Authentication Flow

```
GitHub Actions Workflow
          │
          ▼
GitHub OIDC Token
          │
          ▼
AWS IAM OIDC Provider
          │
          ▼
AWS STS AssumeRoleWithWebIdentity
          │
          ▼
Temporary AWS Credentials
          │
          ▼
Amazon ECR / Amazon EKS
```

### Authentication Methods

| Method                     | Recommendation      |
| -------------------------- | ------------------- |
| Long-lived AWS access keys | Avoid when possible |
| GitHub Actions + AWS OIDC  | Recommended         |
| Temporary AWS credentials  | Preferred           |

Security considerations:

- Restrict the IAM role trust policy to the intended GitHub repository and branch or environment.
- Grant only the AWS permissions required by the workflow.
- Use short-lived credentials.
- Avoid storing AWS access keys in source code.

Important: An AWS IAM role that can update EKS infrastructure does not automatically receive Kubernetes authorization. The CI/CD role will also need appropriate EKS access.

## 🔹 Step 8 — Review the Module 03 Integration Plan

🎯 Objective: Understand the practical sequence for the remaining labs.

| Lab   | Hands-On Activity                                       |
| ----- | ------------------------------------------------------- |
| 03002 | Build a custom Docker image and push it to Docker Hub   |
| 03003 | Deploy the Docker Hub image to EKS                      |
| 03004 | Push the same Docker image to Amazon ECR                |
| 03005 | Deploy the private ECR image to EKS                     |
| 03006 | Configure GitHub repository and AWS OIDC authentication |
| 03007 | Automate Docker Hub image builds and pushes             |
| 03008 | Automate Amazon ECR image builds and pushes             |
| 03009 | Automate EKS deployments and rolling updates            |
| 03010 | Complete end-to-end CI/CD capstone                      |

✅ Expected: You understand how each upcoming lab contributes to the final CI/CD pipeline.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🔎 Verification

| Check                       | Expected Result         |
| --------------------------- | ----------------------- |
| Docker installed            | Yes                     |
| AWS CLI configured          | Yes                     |
| ECR repository `myapp`      | Available               |
| EKS cluster `vyomanant-eks` | Active                  |
| EKS worker nodes            | Ready                   |
| Namespace `vyomanant-lab`   | Available               |
| Docker Hub account          | Accessible              |
| GitHub account              | Accessible              |
| GitHub Actions concepts     | Understood              |
| AWS OIDC authentication     | Understood conceptually |

Lab completion criteria: You understand the integration architecture and have verified the existing resources required for upcoming labs.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🛠️ Troubleshooting

| Issue                          | Possible Cause                                | Resolution                                       |
| ------------------------------ | --------------------------------------------- | ------------------------------------------------ |
| Docker command unavailable     | Docker not installed or PATH issue            | Verify Docker installation                       |
| ECR repository not visible     | Incorrect AWS Region or account               | Confirm the account and Region used in Module 01 |
| EKS cluster not accessible     | Expired credentials or kubeconfig issue       | Reauthenticate and update kubeconfig             |
| GitHub Actions tab unavailable | Repository access or policy restrictions      | Verify repository permissions                    |
| Docker Hub login fails         | Incorrect credentials or account restrictions | Verify authentication through Docker Hub         |

### 🔎 Verify AWS Identity

```
aws sts get-caller-identity
```

### 🔎 Verify Kubernetes Connectivity

```
kubectl get nodes
```

### 🔎 Verify Docker

```
docker info
```

Note: `docker info` requires access to the Docker daemon. If access is denied, check Docker service status and your user permissions.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧹 Cleanup

No cleanup is required.

This lab only explores existing services and resources.

Keep the following:

| Resource                      | Action |
| ----------------------------- | ------ |
| ECR repository `myapp`        | Keep   |
| EKS cluster `vyomanant-eks`   | Keep   |
| Managed node group `workers`  | Keep   |
| Namespace `vyomanant-lab`     | Keep   |
| NGINX Deployment              | Keep   |
| NGINX ClusterIP Service       | Keep   |
| EKS creation/deletion scripts | Keep   |

⚠️ Cost Reminder: Your EKS cluster and EC2 worker nodes continue generating charges while running.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧠 Key Takeaways

1. Docker Hub and Amazon ECR store and distribute container images.
2. Amazon EKS runs containerized applications using Kubernetes.
3. Kubernetes Deployments reference container images stored in registries.
4. GitHub Actions automates container build and deployment workflows.
5. CI/CD pipelines reduce repetitive manual deployment tasks.
6. GitHub OIDC enables AWS authentication without permanent access keys.
7. AWS IAM permissions and Kubernetes authorization are separate concerns.
8. The existing EKS cluster can be reused for the complete integration module.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 📚 Official Documentation

- [Docker Hub Documentation](https://docs.docker.com/docker-hub/?utm_source=chatgpt.com)
- [Amazon ECR Documentation](https://docs.aws.amazon.com/AmazonECR/latest/userguide/what-is-ecr.html?utm_source=chatgpt.com)
- [Amazon EKS Documentation](https://docs.aws.amazon.com/eks/latest/userguide/what-is-eks.html?utm_source=chatgpt.com)
- [GitHub Actions Documentation](https://docs.github.com/en/actions?utm_source=chatgpt.com)
- [GitHub Actions Workflow Syntax](https://docs.github.com/en/actions/reference/workflows-and-actions/workflow-syntax?utm_source=chatgpt.com)
- [Configuring AWS OIDC in GitHub Actions](https://docs.github.com/en/actions/how-tos/secure-your-work/security-harden-deployments/oidc-in-aws?utm_source=chatgpt.com)
- [AWS IAM OIDC Federation](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_providers_create_oidc.html?utm_source=chatgpt.com)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Next Lab: 03002 — Build a Custom Docker Image & Push It to Docker Hub

We will create a simple HTML web application, write a Dockerfile, build a custom Docker image, test it locally, and push it to Docker Hub for deployment on Amazon EKS.
