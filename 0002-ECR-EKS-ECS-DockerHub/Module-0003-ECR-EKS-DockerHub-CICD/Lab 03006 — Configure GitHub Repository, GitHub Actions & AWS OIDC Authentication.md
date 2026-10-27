# 🧪 Lab 03006 — Configure GitHub Repository, GitHub Actions & AWS OIDC Authentication

Create a GitHub repository, configure secure AWS authentication using OpenID Connect (OIDC), and prepare GitHub Actions for automated container deployments to Amazon ECR and EKS.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🌍 Lab Overview

In previous labs, we manually built Docker images, pushed them to Docker Hub and Amazon ECR, and deployed them to Amazon EKS.

Now we will prepare GitHub Actions to automate these operations.

Instead of storing permanent AWS access keys in GitHub, we will configure OIDC authentication so GitHub Actions can request temporary AWS credentials.

| Property          | Details                                          |
| ----------------- | ------------------------------------------------ |
| Lab ID            | 03006                                            |
| Module            | 03 — Container CI/CD Integration                 |
| Difficulty        | Intermediate                                     |
| Duration          | 25–30 minutes                                    |
| Tools             | GitHub, GitHub Actions, AWS IAM, Amazon ECR, EKS |
| AWS Region        | `ap-south-1`                                     |
| GitHub Repository | `vyomanant-web`                                  |
| IAM OIDC Provider | `token.actions.githubusercontent.com`            |
| IAM Role          | `GitHubActionsECRRole`                           |
| Authentication    | GitHub OIDC + AWS STS                            |
| Approach          | GitHub UI + AWS Console                          |

🎯 Real-World Scenario: A DevOps team needs to let its GitHub Actions workflows access AWS securely, without saving long-lived AWS credentials in repository secrets.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🎯 Learning Objectives

By the end of this lab, you will be able to:

1. Create a GitHub repository for a Docker application.
2. Understand GitHub Actions workflow structure.
3. Explain how OIDC authentication works.
4. Configure GitHub as an OIDC identity provider in AWS IAM.
5. Create an IAM role trusted by a specific GitHub repository.
6. Grant permissions to publish images to Amazon ECR.
7. Configure GitHub Actions to request temporary AWS credentials.
8. Verify AWS authentication from a GitHub Actions workflow.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧰 Prerequisites

Ensure you have:

- Completed Labs 03001–03005.
- A GitHub account.
- An AWS account with permission to configure IAM.
- AWS CLI profile `vyomanant`.
- Existing ECR repository `myapp`.
- Existing EKS cluster `vyomanant-eks`.
- Local application files from Lab 03002:
  - `index.html`
  - `Dockerfile`
- Git installed on Ubuntu Linux.

### 🔎 Verify Git

```
git --version
```

### 🔎 Verify AWS Authentication

```
export AWS_PROFILE=vyomanant
```

```
aws sts get-caller-identity
```

✅ Expected: Git and AWS CLI are available, and your AWS identity is displayed.

Important: This lab uses your actual GitHub username and AWS account ID. Replace all example placeholders before saving configurations.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🏗️ Lab Architecture

```
                 GitHub
┌────────────────────────────────────────────┐
│                                            │
│ Repository: vyomanant-web                  │
│                                            │
│ Application Code                           │
│ Dockerfile                                 │
│ GitHub Actions Workflow                    │
│         │                                  │
│         ▼                                  │
│ GitHub-Hosted Runner                       │
│         │                                  │
│         │ Request OIDC Token               │
└─────────┼──────────────────────────────────┘
          │
          ▼
┌────────────────────────────────────────────┐
│ AWS IAM                                    │
│                                            │
│ GitHub OIDC Identity Provider              │
│ token.actions.githubusercontent.com        │
│         │                                  │
│         ▼                                  │
│ IAM Role: GitHubActionsECRRole             │
│         │                                  │
│         ▼                                  │
│ AWS STS Temporary Credentials              │
│         │                                  │
│         ▼                                  │
│ Amazon ECR: myapp                          │
└────────────────────────────────────────────┘

          Future Integration
                  │
                  ▼
             Amazon EKS
             vyomanant-eks
```

Architecture explanation:

- GitHub Actions runs the workflow.
- GitHub issues an OIDC token to the workflow.
- AWS IAM validates the token against the configured trust policy.
- AWS STS issues temporary credentials for the IAM role.
- The role provides access to the designated ECR repository.

Important: EKS deployment permissions will be configured separately in Lab 03009.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🚀 Step-by-Step Instructions

## 🔹 Step 1 — Create a GitHub Repository

🎯 Objective: Prepare a central repository for the application and CI/CD workflows.

1. Open [GitHub](https://github.com/?utm_source=chatgpt.com).
2. Sign in.
3. Select New repository.
4. Enter:

| Setting          | Value           |
| ---------------- | --------------- |
| Repository name  | `vyomanant-web` |
| Visibility       | Public          |
| Add README       | No              |
| Add `.gitignore` | No              |
| License          | None            |

5\. Select Create repository.

✅ Expected: An empty GitHub repository named `vyomanant-web` is created.

Note: Public visibility is convenient for this training, but OIDC also works with private repositories.

## 🔹 Step 2 — Push the Application Files to GitHub

🎯 Objective: Upload the existing application source code.

Navigate to the application directory created in Lab 03002:

```
cd vyomanant-web
```

Initialize Git:

```
git init
```

Configure the main branch:

```
git branch -M main
```

Stage the application files:

```
git add index.html Dockerfile
```

Commit:

```
git commit -m "Add Vyomanant Academy web application"
```

Connect your GitHub repository:

```
git remote add origin https://github.com/YOUR_GITHUB_USERNAME/vyomanant-web.git
```

Push:

```
git push -u origin main
```

Important: Replace `YOUR_GITHUB_USERNAME` with your GitHub username. Configure your Git identity if Git requests it, and use your supported GitHub authentication method.

✅ Expected: The GitHub repository contains:

```
vyomanant-web/
├── Dockerfile
└── index.html
```

## 🔹 Step 3 — Create the GitHub OIDC Provider in AWS

🎯 Objective: Register GitHub as an external identity provider in AWS IAM.

### AWS Console Steps

1. Open AWS Console → IAM.
2. Select Identity providers.
3. Select Add provider.
4. Configure:

| Setting       | Value                                         |
| ------------- | --------------------------------------------- |
| Provider type | OpenID Connect                                |
| Provider URL  | `https://token.actions.githubusercontent.com` |
| Audience      | `sts.amazonaws.com`                           |

5\. Select Add provider.

✅ Expected: The GitHub OIDC provider appears in IAM.

Important: If the provider already exists in your AWS account, reuse it rather than creating a duplicate.

## 🔹 Step 4 — Create an IAM Role for GitHub Actions

🎯 Objective: Allow the intended GitHub repository to request AWS temporary credentials.

### AWS Console Steps

1. Open IAM → Roles.
2. Select Create role.
3. Select Web identity.
4. Choose the GitHub OIDC provider.
5. Select audience `sts.amazonaws.com`.
6. Enter your GitHub username or organization and repository when prompted.
7. Restrict access to the `main` branch.
8. Continue to the permissions step.
9. Create the role with this name:

```
GitHubActionsECRRole
```

### Verify the Trust Policy

Open the role and select Trust relationships → Edit trust policy.

Ensure the policy follows this structure:

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Federated": "arn:aws:iam::123456789012:oidc-provider/token.actions.githubusercontent.com"
      },
      "Action": "sts:AssumeRoleWithWebIdentity",
      "Condition": {
        "StringEquals": {
          "token.actions.githubusercontent.com:aud": "sts.amazonaws.com",
          "token.actions.githubusercontent.com:sub": "repo:YOUR_GITHUB_USERNAME/vyomanant-web:ref:refs/heads/main"
        }
      }
    }
  ]
}
```

Replace:

- `123456789012` with your AWS account ID.
- `YOUR_GITHUB_USERNAME` with your actual GitHub username or organization.

🔐 Security Note: The `sub` condition restricts role assumption to the specified repository and `main` branch. Do not replace it with a broad wildcard.

✅ Expected: The role trusts GitHub OIDC tokens from the intended repository branch.

## 🔹 Step 5 — Configure ECR Permissions

🎯 Objective: Grant GitHub Actions permission to push container images to the existing ECR repository.

Open IAM → Roles → GitHubActionsECRRole.

Select Add permissions → Create inline policy.

Use the JSON editor and add:

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "ECRAuthentication",
      "Effect": "Allow",
      "Action": [
        "ecr:GetAuthorizationToken"
      ],
      "Resource": "*"
    },
    {
      "Sid": "ECRImagePush",
      "Effect": "Allow",
      "Action": [
        "ecr:BatchCheckLayerAvailability",
        "ecr:CompleteLayerUpload",
        "ecr:InitiateLayerUpload",
        "ecr:PutImage",
        "ecr:UploadLayerPart",
        "ecr:BatchGetImage"
      ],
      "Resource": "arn:aws:ecr:ap-south-1:123456789012:repository/myapp"
    }
  ]
}
```

Replace `123456789012` with your AWS account ID.

Save the policy as:

```
GitHubActionsECRPushPolicy
```

🧠 Why It Matters: The workflow receives permissions to authenticate and push images to `myapp`, rather than broad administrator access.

Important: `ecr:GetAuthorizationToken` requires `Resource: "*"`. The repository-specific actions remain restricted to `myapp`.

## 🔹 Step 6 — Add the AWS Role ARN to GitHub

🎯 Objective: Store the role identifier for use in GitHub Actions.

In AWS IAM:

1. Open Roles → GitHubActionsECRRole.
2. Copy the ARN.

Example:

```
arn:aws:iam::123456789012:role/GitHubActionsECRRole
```

In GitHub:

1. Open `vyomanant-web`.
2. Select Settings.
3. Navigate to Secrets and variables → Actions.
4. Open Variables.
5. Select New repository variable.
6. Configure:

| Name           | Value             |
| -------------- | ----------------- |
| `AWS_ROLE_ARN` | Your IAM role ARN |

7\. Save.

Important: The role ARN is an identifier, not a password. A GitHub Actions variable is appropriate for this value.

## 🔹 Step 7 — Create a GitHub Actions Authentication Workflow

🎯 Objective: Test GitHub-to-AWS OIDC authentication.

In your local application directory, create the workflow directory:

```
mkdir -p .github/workflows
```

Create the workflow:

```
nano .github/workflows/aws-auth-test.yml
```

Add:

```
name: AWS OIDC Authentication Test

on:
  workflow_dispatch:

permissions:
  id-token: write
  contents: read

jobs:
  test-aws-auth:
    runs-on: ubuntu-latest

    steps:
      - name: Configure AWS Credentials
        uses: aws-actions/configure-aws-credentials@v4
        with:
          role-to-assume: ${{ vars.AWS_ROLE_ARN }}
          aws-region: ap-south-1

      - name: Verify AWS Identity
        run: aws sts get-caller-identity

      - name: Verify ECR Repository
        run: aws ecr describe-repositories --repository-names myapp --region ap-south-1
```

### Workflow Explanation

| Component                   | Purpose                                  |
| --------------------------- | ---------------------------------------- |
| `workflow_dispatch`         | Allows manual execution                  |
| `id-token: write`           | Permits requesting a GitHub OIDC token   |
| `contents: read`            | Grants read access to repository content |
| `configure-aws-credentials` | Assumes the IAM role using OIDC          |
| `sts get-caller-identity`   | Verifies the AWS identity                |
| `ecr describe-repositories` | Tests ECR read access                    |

⚠️ Technical Note: The ECR policy from Step 5 grants image push permissions but does not include `ecr:DescribeRepositories`. For this verification workflow, add the following permission to the repository-scoped action list in `GitHubActionsECRPushPolicy`:

```
"ecr:DescribeRepositories"
```

This permission must be added before running the workflow.

Security note: For production workflows, pin third-party GitHub Actions to reviewed commit SHAs instead of relying only on major-version tags.

## 🔹 Step 8 — Push and Run the GitHub Actions Workflow

🎯 Objective: Execute the workflow from GitHub.

Stage the workflow:

```
git add .github/workflows/aws-auth-test.yml
```

Commit:

```
git commit -m "Add AWS OIDC authentication workflow"
```

Push:

```
git push
```

### Run from GitHub

1. Open the `vyomanant-web` repository.
2. Select Actions.
3. Open AWS OIDC Authentication Test.
4. Select Run workflow.
5. Choose branch `main`.
6. Select Run workflow.
7. Open the workflow run and inspect the job logs.

✅ Expected: The workflow completes successfully.

The identity verification step should show an assumed-role identity similar to:

```
arn:aws:sts::123456789012:assumed-role/GitHubActionsECRRole/GitHubActions
```

Note: The exact session name may differ.

The ECR verification step should display repository details for `myapp`.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🔎 Verification

| Check                               | Expected Result                     |
| ----------------------------------- | ----------------------------------- |
| GitHub repository                   | `vyomanant-web`                     |
| Application files                   | Uploaded                            |
| GitHub OIDC provider                | Configured                          |
| IAM role                            | `GitHubActionsECRRole`              |
| Trust policy                        | Restricted to repository and `main` |
| ECR permissions                     | Scoped to `myapp`                   |
| GitHub variable                     | `AWS_ROLE_ARN`                      |
| GitHub Actions workflow             | Created                             |
| AWS authentication test             | Successful                          |
| ECR repository verification         | Successful                          |
| Permanent AWS access keys in GitHub | None                                |

Lab completion criteria: GitHub Actions successfully authenticates to AWS using OIDC and accesses the designated ECR repository.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🛠️ Troubleshooting

| Issue                                                     | Possible Cause                                       | Resolution                            |
| --------------------------------------------------------- | ---------------------------------------------------- | ------------------------------------- |
| `Not authorized to perform sts:AssumeRoleWithWebIdentity` | Incorrect trust policy or GitHub repository/branch   | Verify the `aud` and `sub` conditions |
| `Could not load credentials`                              | Missing or incorrect `AWS_ROLE_ARN`                  | Check the GitHub repository variable  |
| `AccessDeniedException` for ECR                           | Missing IAM permission                               | Review the inline ECR policy          |
| Workflow not visible                                      | Workflow file not on the default branch              | Verify the file path and push         |
| Git push rejected                                         | GitHub authentication or repository permission issue | Verify Git remote and GitHub access   |

### 🔎 Verify IAM Role in AWS CLI

```
aws iam get-role --role-name GitHubActionsECRRole
```

Expected: AWS returns the IAM role configuration.

Important: GitHub OIDC authentication does not require storing AWS access keys in GitHub repository secrets.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧹 Cleanup

Keep the resources for upcoming CI/CD labs.

| Resource                          | Action |
| --------------------------------- | ------ |
| GitHub repository `vyomanant-web` | Keep   |
| GitHub Actions workflow           | Keep   |
| GitHub OIDC provider              | Keep   |
| IAM role `GitHubActionsECRRole`   | Keep   |
| IAM inline policy                 | Keep   |
| GitHub variable `AWS_ROLE_ARN`    | Keep   |
| ECR repository `myapp`            | Keep   |
| EKS cluster `vyomanant-eks`       | Keep   |

No cleanup is required now.

⚠️ Security Reminder: Review and remove unused IAM roles and OIDC trust relationships when the training project is permanently retired.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧠 Key Takeaways

1. GitHub Actions automates application build and deployment tasks.
2. OIDC enables secure authentication between GitHub and AWS.
3. AWS STS issues temporary credentials to authorized workflows.
4. IAM trust policies control which repositories and branches can assume roles.
5. IAM permissions policies control what authenticated workflows can do.
6. GitHub repository variables can store non-sensitive configuration such as IAM role ARNs.
7. GitHub Actions can access Amazon ECR without permanent AWS access keys.
8. EKS deployment access requires additional configuration in a later lab.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 📚 Official Documentation

- [GitHub Actions Documentation](https://docs.github.com/en/actions?utm_source=chatgpt.com)
- [GitHub Actions Workflow Syntax](https://docs.github.com/en/actions/reference/workflows-and-actions/workflow-syntax?utm_source=chatgpt.com)
- [Configuring OpenID Connect in AWS](https://docs.github.com/en/actions/how-tos/secure-your-work/security-harden-deployments/oidc-in-aws?utm_source=chatgpt.com)
- [AWS IAM OIDC Identity Providers](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_providers_create_oidc.html?utm_source=chatgpt.com)
- [AWS IAM Roles for OIDC](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-idp_oidc.html?utm_source=chatgpt.com)
- [Amazon ECR IAM Permissions](https://docs.aws.amazon.com/AmazonECR/latest/userguide/security_iam_id-based-policy-examples.html?utm_source=chatgpt.com)
- [AWS Configure Credentials GitHub Action](https://github.com/aws-actions/configure-aws-credentials?utm_source=chatgpt.com)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Next Lab: 03007 — Automate Docker Build & Push to Docker Hub Using GitHub Actions

We will create a GitHub Actions workflow that automatically builds the Vyomanant Academy Docker image and publishes it to Docker Hub whenever application code is pushed to the `main` branch.
