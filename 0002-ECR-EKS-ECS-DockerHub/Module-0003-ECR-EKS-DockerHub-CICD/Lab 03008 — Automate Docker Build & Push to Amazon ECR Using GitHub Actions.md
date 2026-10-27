# 🧪 Lab 03008 — Automate Docker Build & Push to Amazon ECR Using GitHub Actions

Create a GitHub Actions CI pipeline that automatically builds the Vyomanant Academy Docker image and pushes it to a private Amazon ECR repository using secure AWS OIDC authentication.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🌍 Lab Overview

In Lab 03007, we automated Docker image builds and pushes to Docker Hub.

Now we will extend our CI pipeline to Amazon Elastic Container Registry (ECR).

GitHub Actions will automatically build the application image and publish it to Amazon ECR whenever relevant application files are pushed to the `main` branch.

We will reuse the AWS OIDC authentication configured in Lab 03006.

| Property          | Details                                     |
| ----------------- | ------------------------------------------- |
| Lab ID            | 03008                                       |
| Module            | 03 — Container CI/CD Integration            |
| Difficulty        | Intermediate                                |
| Duration          | 25–30 minutes                               |
| Tools             | GitHub Actions, Docker, AWS IAM, Amazon ECR |
| GitHub Repository | `vyomanant-web`                             |
| AWS Region        | `ap-south-1`                                |
| ECR Repository    | `myapp`                                     |
| Workflow File     | `ecr-ci.yml`                                |
| Authentication    | GitHub OIDC                                 |
| Image Tag         | `web-v2`                                    |
| Trigger           | Push to `main`                              |

🎯 Real-World Scenario: A DevOps team wants application changes automatically packaged and published to its private AWS container registry without managing permanent AWS access keys.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🎯 Learning Objectives

By the end of this lab, you will be able to:

1. Reuse GitHub OIDC authentication for AWS.
2. Understand how GitHub Actions authenticates to ECR.
3. Create an automated Docker build workflow.
4. Push images to a private ECR repository.
5. Use an image tag compatible with an immutable ECR repository.
6. Monitor GitHub Actions execution.
7. Verify published images in Amazon ECR.
8. Prepare container images for automated EKS deployments.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧰 Prerequisites

Ensure you have:

- Completed Lab 03007 — Automate Docker Build & Push to Docker Hub.
- GitHub repository `vyomanant-web`.
- Existing AWS OIDC provider.
- IAM role `GitHubActionsECRRole`.
- GitHub Actions variable `AWS_ROLE_ARN`.
- ECR repository `myapp` in `ap-south-1`.
- IAM permissions to push images to `myapp`.
- Application files `Dockerfile` and `index.html`.
- Git installed and configured.

### 🔎 Verify the Local Repository

Navigate to your application directory:

```
cd vyomanant-web
```

```
git status
```

✅ Expected: You are working in the Git repository containing the application files.

Important: This lab assumes the application displays Version v2, as configured in Lab 03007.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🏗️ Lab Architecture

```
                  Developer
                      │
                      │ git push
                      ▼
          ┌──────────────────────────┐
          │ GitHub Repository        │
          │ vyomanant-web            │
          │                          │
          │ Dockerfile               │
          │ index.html               │
          └────────────┬─────────────┘
                       │
                       ▼
          ┌──────────────────────────┐
          │ GitHub Actions           │
          │ ecr-ci.yml               │
          │                          │
          │ 1. Checkout Code         │
          │ 2. Authenticate AWS      │
          │ 3. Login to ECR          │
          │ 4. Build Docker Image    │
          │ 5. Push Image            │
          └────────────┬─────────────┘
                       │
                       │ GitHub OIDC
                       ▼
          ┌──────────────────────────┐
          │ AWS IAM + STS            │
          │ GitHubActionsECRRole     │
          │ Temporary Credentials    │
          └────────────┬─────────────┘
                       │
                       ▼
          ┌──────────────────────────┐
          │ Amazon ECR               │
          │                          │
          │ Repository: myapp        │
          │ Image: web-v2            │
          └──────────────────────────┘
```

Architecture explanation:

- A Git push triggers the GitHub Actions workflow.
- GitHub Actions requests temporary AWS credentials using OIDC.
- The workflow authenticates Docker to Amazon ECR.
- Docker builds the application and pushes it to the private ECR repository.
- The published image will be used for EKS deployment in Lab 03009.

Note: This lab does not modify the existing EKS Deployment.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🚀 Step-by-Step Instructions

## 🔹 Step 1 — Verify the Existing ECR Repository

🎯 Objective: Confirm that the private ECR repository is available.

### AWS Console Steps

1. Open the [AWS Management Console](https://console.aws.amazon.com/?utm_source=chatgpt.com).
2. Select Region Mumbai (`ap-south-1`).
3. Navigate to Amazon ECR → Private registry → Repositories.
4. Open `myapp`.
5. Review the existing image tags.

Expected existing images:

| Image Tag | Source                      |
| --------- | --------------------------- |
| `v1`      | Module 01                   |
| `web-v1`  | Lab 03004                   |
| `web-v2`  | Will be created in this lab |

Important: Confirm that `web-v2` does not already exist. Our ECR repository uses immutable image tags, so an existing tag cannot be overwritten.

## 🔹 Step 2 — Verify GitHub OIDC Configuration

🎯 Objective: Confirm that GitHub Actions can authenticate to AWS.

Open your GitHub repository:

Settings → Secrets and variables → Actions → Variables

Verify:

| Variable       | Expected Value                |
| -------------- | ----------------------------- |
| `AWS_ROLE_ARN` | ARN of `GitHubActionsECRRole` |

### AWS Console Verification

1. Open IAM → Roles.
2. Select `GitHubActionsECRRole`.
3. Review the Trust relationships tab.
4. Confirm that the role trusts the intended GitHub repository and `main` branch.
5. Review the ECR permissions policy.

🔐 Security Note: Do not create AWS access keys or store permanent AWS credentials in GitHub.

## 🔹 Step 3 — Understand the ECR Image Tagging Strategy

🎯 Objective: Choose a version tag that works with the existing immutable ECR repository.

We will publish the application as:

```
123456789012.dkr.ecr.ap-south-1.amazonaws.com/myapp:web-v2
```

Replace `123456789012` with your actual AWS account ID.

### Image Version Comparison

| Image          | Application Version |
| -------------- | ------------------- |
| `myapp:web-v1` | v1                  |
| `myapp:web-v2` | v2                  |

🧠 Why It Matters: Versioned image tags make it easier to identify releases and perform Kubernetes rolling updates.

Technical Note: This lab uses a fixed tag, `web-v2`, for simplicity. Production CI pipelines should generate unique image tags, such as commit SHAs, to avoid conflicts with immutable repositories.

## 🔹 Step 4 — Create the GitHub Actions ECR Workflow

🎯 Objective: Automate Docker image building and publishing to Amazon ECR.

Create the workflow file:

```
nano .github/workflows/ecr-ci.yml
```

Add the following YAML:

```
name: Amazon ECR CI Pipeline

on:
  push:
    branches:
      - main
    paths:
      - Dockerfile
      - index.html
      - .github/workflows/ecr-ci.yml

  workflow_dispatch:

permissions:
  id-token: write
  contents: read

jobs:
  ecr-build-push:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Repository
        uses: actions/checkout@v4

      - name: Configure AWS Credentials
        uses: aws-actions/configure-aws-credentials@v4
        with:
          role-to-assume: ${{ vars.AWS_ROLE_ARN }}
          aws-region: ap-south-1

      - name: Login to Amazon ECR
        id: login-ecr
        uses: aws-actions/amazon-ecr-login@v2

      - name: Build and Push Docker Image
        env:
          ECR_REGISTRY: ${{ steps.login-ecr.outputs.registry }}
          ECR_REPOSITORY: myapp
          IMAGE_TAG: web-v2
        run: |
          docker build -t $ECR_REGISTRY/$ECR_REPOSITORY:$IMAGE_TAG .
          docker push $ECR_REGISTRY/$ECR_REPOSITORY:$IMAGE_TAG
```

### Workflow Explanation

| Component                   | Purpose                            |
| --------------------------- | ---------------------------------- |
| `push`                      | Runs on relevant changes to `main` |
| `workflow_dispatch`         | Enables manual execution           |
| `id-token: write`           | Allows GitHub OIDC authentication  |
| `actions/checkout`          | Retrieves application source code  |
| `configure-aws-credentials` | Assumes the AWS IAM role           |
| `amazon-ecr-login`          | Authenticates Docker to ECR        |
| `docker build`              | Creates the application image      |
| `docker push`               | Publishes the image to ECR         |

⚠️ Important: Because `web-v2` is immutable, run this workflow only once for this tag. Repeated runs will fail when attempting to push an existing image manifest.

Production Best Practice: Pin third-party GitHub Actions to reviewed commit SHAs.

## 🔹 Step 5 — Commit and Push the Workflow

🎯 Objective: Trigger the ECR CI pipeline.

Stage the workflow:

```
git add .github/workflows/ecr-ci.yml
```

Commit:

```
git commit -m "Add Amazon ECR CI pipeline"
```

Push:

```
git push
```

✅ Expected: GitHub receives the workflow file and automatically starts the ECR CI pipeline.

Important: The existing Docker Hub workflow from Lab 03007 uses path filters, so this ECR workflow change should not trigger another Docker Hub image build.

## 🔹 Step 6 — Monitor the GitHub Actions Workflow

🎯 Objective: Verify that the automated build and push complete successfully.

### GitHub Steps

1. Open your `vyomanant-web` repository.
2. Select Actions.
3. Open Amazon ECR CI Pipeline.
4. Select the latest workflow run.
5. Open the `ecr-build-push` job.

### Expected Workflow

```
Checkout Repository
        │
        ▼
Configure AWS Credentials
        │
        ▼
Login to Amazon ECR
        │
        ▼
Build and Push Docker Image
        │
        ▼
Workflow Completed
```

✅ Expected: All workflow steps complete successfully.

### Expected Docker Push Output

```
The push refers to repository
[123456789012.dkr.ecr.ap-south-1.amazonaws.com/myapp]

web-v2: digest: sha256:... size: ...
```

🔎 Observation: GitHub Actions builds and publishes the Docker image without requiring local Docker commands or permanent AWS credentials.

## 🔹 Step 7 — Verify the Published Image in AWS Console

🎯 Objective: Confirm that the image is stored in Amazon ECR.

1. Open AWS Console → Amazon ECR.
2. Select Private registry → Repositories.
3. Open `myapp`.
4. Select the Images tab.
5. Locate the `web-v2` tag.

### Expected Image Details

| Property     | Expected       |
| ------------ | -------------- |
| Repository   | `myapp`        |
| Image tag    | `web-v2`       |
| Registry     | Private ECR    |
| Region       | `ap-south-1`   |
| Image digest | SHA-256 digest |
| Source       | GitHub Actions |

✅ Expected: The image tagged `web-v2` is visible in the repository.

## 🔹 Step 8 — Verify the ECR Image Using AWS CLI

🎯 Objective: Confirm that the image is available for Kubernetes deployment.

Set your AWS profile:

```
export AWS_PROFILE=vyomanant
```

Verify the image:

```
aws ecr describe-images --repository-name myapp --image-ids imageTag=web-v2 --region ap-south-1
```

Expected (illustrative):

```
{
    "imageDetails": [
        {
            "registryId": "123456789012",
            "repositoryName": "myapp",
            "imageTags": [
                "web-v2"
            ],
            "imageDigest": "sha256:..."
        }
    ]
}
```

✅ Expected: AWS returns the image details for `web-v2`.

🧠 Why It Matters: This confirms that the image is available in the private registry and ready for deployment to EKS.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🔎 Verification

| Check                    | Expected Result        |
| ------------------------ | ---------------------- |
| GitHub repository        | `vyomanant-web`        |
| Workflow file            | `ecr-ci.yml`           |
| AWS authentication       | GitHub OIDC            |
| IAM role                 | `GitHubActionsECRRole` |
| ECR repository           | `myapp`                |
| Docker build             | Successful             |
| Docker push              | Successful             |
| Image tag                | `web-v2`               |
| GitHub Actions run       | Successful             |
| ECR Console verification | Image visible          |
| AWS CLI verification     | Image details returned |
| Existing EKS Deployment  | Unchanged              |

Lab completion criteria: GitHub Actions successfully builds the Docker image and publishes `myapp:web-v2` to Amazon ECR using temporary AWS credentials.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🛠️ Troubleshooting

| Issue                               | Possible Cause                         | Resolution                                           |
| ----------------------------------- | -------------------------------------- | ---------------------------------------------------- |
| OIDC authentication fails           | Incorrect IAM trust policy or role ARN | Verify GitHub repository, branch, and `AWS_ROLE_ARN` |
| ECR login fails                     | Missing ECR authentication permission  | Verify `ecr:GetAuthorizationToken`                   |
| Docker push denied                  | Missing ECR repository permissions     | Review `GitHubActionsECRPushPolicy`                  |
| Push fails with immutable tag error | `web-v2` already exists                | Use a new unused image tag                           |
| Workflow does not start             | Incorrect branch or path filter        | Verify the workflow location and trigger             |

### 🔎 Verify the IAM Role

```
aws iam get-role --role-name GitHubActionsECRRole
```

### 🔎 Verify the ECR Repository

```
aws ecr describe-repositories --repository-names myapp --region ap-south-1
```

Important: Review the failed GitHub Actions step before modifying IAM permissions or workflow configuration.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧹 Cleanup

Keep all resources for the upcoming EKS deployment automation lab.

| Resource                          | Action |
| --------------------------------- | ------ |
| GitHub repository `vyomanant-web` | Keep   |
| Workflow `ecr-ci.yml`             | Keep   |
| IAM role `GitHubActionsECRRole`   | Keep   |
| GitHub OIDC provider              | Keep   |
| ECR repository `myapp`            | Keep   |
| ECR image `web-v1`                | Keep   |
| ECR image `web-v2`                | Keep   |
| EKS cluster `vyomanant-eks`       | Keep   |
| Deployment `vyomanant-web`        | Keep   |

No cleanup is required.

⚠️ Cost Reminder: ECR image storage, the EKS control plane, and EC2 worker nodes may continue generating AWS charges.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧠 Key Takeaways

1. GitHub Actions can automatically build Docker images.
2. GitHub OIDC provides temporary AWS credentials without permanent access keys.
3. AWS IAM roles control GitHub Actions access to AWS resources.
4. Amazon ECR stores container images in private repositories.
5. `amazon-ecr-login` simplifies Docker authentication to ECR.
6. Versioned image tags support controlled application releases.
7. Immutable ECR tags prevent accidental replacement of published images.
8. Automated ECR image publishing prepares applications for EKS CI/CD deployment.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 📚 Official Documentation

- [GitHub Actions Documentation](https://docs.github.com/en/actions?utm_source=chatgpt.com)
- [GitHub Actions Workflow Syntax](https://docs.github.com/en/actions/reference/workflows-and-actions/workflow-syntax?utm_source=chatgpt.com)
- [GitHub OIDC Authentication with AWS](https://docs.github.com/en/actions/how-tos/secure-your-work/security-harden-deployments/oidc-in-aws?utm_source=chatgpt.com)
- [AWS Configure Credentials GitHub Action](https://github.com/aws-actions/configure-aws-credentials?utm_source=chatgpt.com)
- [Amazon ECR Login GitHub Action](https://github.com/aws-actions/amazon-ecr-login?utm_source=chatgpt.com)
- [Pushing Docker Images to Amazon ECR](https://docs.aws.amazon.com/AmazonECR/latest/userguide/docker-push-ecr-image.html?utm_source=chatgpt.com)
- [Amazon ECR Image Tag Mutability](https://docs.aws.amazon.com/AmazonECR/latest/userguide/image-tag-mutability.html?utm_source=chatgpt.com)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Next Lab: 03009 — Automate EKS Deployment & Rolling Updates Using GitHub Actions

We will configure secure EKS access for GitHub Actions, automate Kubernetes Deployment updates using the image published to Amazon ECR, and verify rolling updates on the existing EKS cluster.
