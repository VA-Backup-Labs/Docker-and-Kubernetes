# 🧪 Lab 01001 — AWS ECR Fundamentals, IAM Permissions & Prerequisites

Learn the fundamentals of Amazon Elastic Container Registry (ECR), verify AWS access, and prepare your environment for managing Docker images.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🌍 Lab Overview

Amazon Elastic Container Registry (ECR) is a fully managed AWS container image registry used to store, manage, and distribute container images.

In this lab, you will explore ECR through the AWS Management Console, understand the required IAM permissions, and verify the tools needed for upcoming hands-on labs.

| Property              | Details                      |
| --------------------- | ---------------------------- |
| Lab ID                | 01001                        |
| Module                | 01 — Amazon ECR              |
| Difficulty            | Beginner                     |
| Duration              | 20–25 minutes                |
| Tools                 | AWS Console, Docker, AWS CLI |
| Environment           | AWS Account + Local Machine  |
| Approach              | Console-first, minimal CLI   |
| AWS Resources Created | None                         |

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🎯 Learning Objectives

By the end of this lab, you will be able to:

1. Explain the purpose of Amazon ECR.
2. Differentiate between public and private ECR repositories.
3. Navigate the Amazon ECR console.
4. Identify the IAM permissions required for ECR operations.
5. Understand ECR repositories, images, and image tags.
6. Verify Docker and AWS CLI installation.
7. Identify your AWS account and Region for upcoming labs.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧰 Prerequisites

Ensure you have:

- An active AWS account.
- AWS Management Console access.
- IAM permissions to view ECR and IAM configuration.
- Docker installed and running.
- AWS CLI v2 installed.
- A terminal such as Bash, PowerShell, or Git Bash.

Note: The commands in this lab use a Bash-compatible terminal.

### 🔎 Verify Docker

```
docker --version
```

✅ Expected: Docker version information is displayed.

### 🔎 Verify AWS CLI

```
aws --version
```

✅ Expected: AWS CLI version information is displayed.

If a tool is missing, install it using the official documentation linked at the end of this lab.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🏗️ Lab Architecture

```
┌─────────────────────────────┐
│        AWS Account          │
│                             │
│  ┌───────────────────────┐  │
│  │  IAM Identity         │  │
│  │  ECR Permissions      │  │
│  └───────────┬───────────┘  │
│              │              │
│              ▼              │
│  ┌───────────────────────┐  │
│  │      Amazon ECR       │  │
│  │                       │  │
│  │  Private Repositories │  │
│  │  Public Repositories  │  │
│  └───────────────────────┘  │
│                             │
└─────────────────────────────┘

        Local Machine
┌─────────────────────────────┐
│  Docker CLI                 │
│  AWS CLI                    │
│  Bash Terminal              │
└─────────────────────────────┘
```

Architecture explanation: This is a conceptual architecture. In this lab, you will inspect AWS access and ECR without creating repositories or pushing images.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🚀 Step-by-Step Instructions

## 🔹 Step 1 — Sign In to the AWS Management Console

🎯 Objective: Access your AWS environment.

1. Open [AWS Management Console](https://console.aws.amazon.com/?utm_source=chatgpt.com).
2. Sign in using your authorized IAM user or IAM Identity Center credentials.
3. Select the AWS Region you will use throughout the training.
4. Record your AWS account ID and selected Region.

✅ Expected: You can access the AWS Console and identify the current AWS Region.

🧠 Why It Matters: ECR private repositories are Region-specific. Consistently using one Region avoids image URI and deployment confusion.

## 🔹 Step 2 — Explore Amazon ECR

🎯 Objective: Locate Amazon ECR and understand its main features.

1. In the AWS Console search bar, search for ECR.
2. Select Elastic Container Registry.
3. Explore the Private registry area.
4. Locate Repositories.
5. Explore the Public registry area if available.

### Key ECR Components

| Component        | Purpose                                             |
| ---------------- | --------------------------------------------------- |
| Registry         | Hosts container image repositories                  |
| Repository       | Stores container images                             |
| Image            | Packaged application and dependencies               |
| Image Tag        | Human-readable image version identifier             |
| Image Digest     | Content-addressed image identifier                  |
| Lifecycle Policy | Automatically expires images matching defined rules |
| Image Scanning   | Helps identify image vulnerabilities                |

✅ Expected: You can navigate between ECR registry and repository sections.

## 🔹 Step 3 — Understand Private vs Public ECR

🎯 Objective: Identify which registry type is suitable for enterprise deployments.

| Feature                     | Private ECR                   | Public ECR                   |
| --------------------------- | ----------------------------- | ---------------------------- |
| Primary purpose             | Controlled image distribution | Public image distribution    |
| Access control              | IAM and repository policies   | Public image pulls supported |
| Typical usage               | Internal applications         | Publicly shared images       |
| EKS integration             | Supported                     | Supported                    |
| Recommended for this course | Yes                           | Optional                     |

🧠 Why It Matters: We will use private ECR repositories for the main AWS EKS integration labs.

## 🔹 Step 4 — Explore IAM Permissions for ECR

🎯 Objective: Understand how AWS controls access to ECR.

1. Open AWS Console → IAM.
2. Select Policies.
3. Search for `AmazonEC2ContainerRegistryReadOnly`.
4. Open the policy and review its permissions.
5. Search for `AmazonEC2ContainerRegistryPowerUser`.
6. Review the permissions provided by this policy.

### Common ECR Permissions

| IAM Permission              | Purpose                      |
| --------------------------- | ---------------------------- |
| `ecr:GetAuthorizationToken` | Authenticate a Docker client |
| `ecr:CreateRepository`      | Create an ECR repository     |
| `ecr:DescribeRepositories`  | View repository details      |
| `ecr:PutImage`              | Publish image manifests      |
| `ecr:BatchGetImage`         | Retrieve image manifests     |
| `ecr:BatchDeleteImage`      | Delete images                |

⚠️ Security: Do not attach broad IAM policies unnecessarily. Your training identity should receive only the permissions required for the exercises. Viewing policies does not grant permissions.

✅ Expected: You understand which IAM actions support common ECR tasks.

## 🔹 Step 5 — Verify AWS CLI Authentication

🎯 Objective: Confirm that your terminal can access AWS using an authorized identity.

If AWS CLI is not yet configured, use the authentication method approved for your account. For IAM Identity Center environments, follow the AWS CLI SSO setup documentation.

Run:

```
aws sts get-caller-identity
```

✅ Expected (illustrative):

```
{
    "UserId": "EXAMPLEUSERID",
    "Account": "123456789012",
    "Arn": "arn:aws:iam::123456789012:user/lab-user"
}
```

Your actual output will depend on your AWS identity.

🧠 Why It Matters: This command verifies the identity used by AWS CLI before Docker authentication and EKS cluster creation.

## 🔹 Step 6 — Understand ECR Image Naming

🎯 Objective: Learn how Docker images are identified in private ECR.

An ECR image URI follows this format:

```
<account-id>.dkr.ecr.<region>.amazonaws.com/<repository-name>:<tag>
```

Example (illustrative):

```
123456789012.dkr.ecr.ap-south-1.amazonaws.com/myapp:v1
```

| URI Component  | Example        |
| -------------- | -------------- |
| AWS Account ID | `123456789012` |
| AWS Region     | `ap-south-1`   |
| Repository     | `myapp`        |
| Image Tag      | `v1`           |

✅ Expected: You can identify the registry, repository, and tag from an ECR image URI.

## 🔹 Step 7 — Verify Docker Engine Availability

🎯 Objective: Confirm Docker is ready for future image build and push operations.

```
docker info
```

✅ Expected: Docker client and server information is displayed without a connection error.

If Docker Desktop is installed, ensure it is running.

🧠 Why It Matters: Upcoming labs will build and push container images from your local Docker environment.

## 🔹 Step 8 — Review the Upcoming Deployment Workflow

🎯 Objective: Understand how ECR fits into the complete learning path.

```
Developer
    │
    ▼
Docker Image Build
    │
    ▼
Amazon ECR
    │
    ▼
Amazon EKS
    │
    ▼
Kubernetes Application
    │
    ▼
End Users
```

Later modules will introduce Docker Hub and GitHub Actions to automate image publishing and EKS deployments.

✅ Expected: You understand where ECR fits into the application delivery process.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🔎 Verification

Use the following checklist to confirm completion.

| Verification             | Expected Result              |
| ------------------------ | ---------------------------- |
| AWS Console access       | Successful                   |
| AWS Region identified    | Yes                          |
| ECR Console navigation   | Successful                   |
| IAM policies reviewed    | Completed                    |
| AWS CLI authentication   | Valid AWS identity returned  |
| Docker version check     | Version displayed            |
| Docker Engine check      | Server information displayed |
| ECR image URI understood | Yes                          |

Lab completion criteria: Your AWS identity, Docker environment, and AWS CLI are ready for the next ECR lab.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🛠️ Troubleshooting

| Issue                          | Possible Cause                           | Resolution                                       |
| ------------------------------ | ---------------------------------------- | ------------------------------------------------ |
| AWS Console access denied      | Missing permissions                      | Contact your AWS administrator                   |
| ECR repositories not visible   | Wrong Region or insufficient permissions | Check selected Region and IAM permissions        |
| `aws: command not found`       | AWS CLI not installed                    | Install AWS CLI v2                               |
| `Unable to locate credentials` | CLI authentication not configured        | Configure the approved AWS authentication method |
| Docker daemon connection error | Docker Engine not running                | Start Docker Desktop or Docker Engine            |

### 🔎 Check AWS CLI Configuration

```
aws configure list
```

This displays the active CLI configuration sources without printing secret access key values.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧹 Cleanup

No AWS resources were created in this lab.

No cleanup is required. Keep Docker and AWS CLI installed for subsequent labs.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧠 Key Takeaways

- Amazon ECR stores and distributes container images.
- Private ECR repositories are commonly used for enterprise applications.
- IAM controls access to ECR operations.
- ECR repositories are associated with AWS Regions.
- Docker CLI is used to build, tag, push, and pull images.
- AWS CLI enables authenticated interactions with AWS services.
- ECR images can be deployed to Kubernetes workloads running on EKS.
- GitHub Actions will later automate image publishing and deployment.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 📚 Official Documentation

- [Amazon ECR User Guide](https://docs.aws.amazon.com/AmazonECR/latest/userguide/what-is-ecr.html?utm_source=chatgpt.com)
- [Amazon ECR IAM Policies](https://docs.aws.amazon.com/AmazonECR/latest/userguide/security_iam_id-based-policy-examples.html?utm_source=chatgpt.com)
- [Amazon ECR Private Repositories](https://docs.aws.amazon.com/AmazonECR/latest/userguide/Repositories.html?utm_source=chatgpt.com)
- [Install AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html?utm_source=chatgpt.com)
- [Configure AWS CLI with IAM Identity Center](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-sso.html?utm_source=chatgpt.com)
- [Install Docker Engine](https://docs.docker.com/engine/install/?utm_source=chatgpt.com)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Next Lab: 01002 — Create and Configure an Amazon ECR Private Repository Using AWS Console
