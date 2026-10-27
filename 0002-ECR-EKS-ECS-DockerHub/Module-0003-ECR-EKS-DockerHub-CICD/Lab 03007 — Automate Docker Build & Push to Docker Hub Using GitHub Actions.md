# 🧪 Lab 03007 — Automate Docker Build & Push to Docker Hub Using GitHub Actions

Build a CI pipeline that automatically builds your Vyomanant Academy Docker image and pushes it to Docker Hub whenever code is pushed to GitHub.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🌍 Lab Overview

In Lab 03006, we configured a GitHub repository and tested GitHub Actions authentication with AWS using OIDC.

In this lab, we will automate the Docker image build and push process using GitHub Actions.

Whenever a developer pushes changes to the `main` branch, GitHub Actions will build the Docker image and publish it to Docker Hub.

| Property              | Details                            |
| --------------------- | ---------------------------------- |
| Lab ID                | 03007                              |
| Module                | 03 — Container CI/CD Integration   |
| Difficulty            | Beginner–Intermediate              |
| Duration              | 20–30 minutes                      |
| Tools                 | GitHub Actions, Docker, Docker Hub |
| GitHub Repository     | `vyomanant-web`                    |
| Docker Hub Repository | `vyomanant-web`                    |
| Workflow File         | `dockerhub-ci.yml`                 |
| Trigger               | Push to `main`                     |
| Image Tags            | `latest`, `v2`                     |
| Authentication        | Docker Hub Access Token            |

🎯 Real-World Scenario: A development team wants every code change to automatically produce a new container image without manually running `docker build` and `docker push`.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🎯 Learning Objectives

By the end of this lab, you will be able to:

1. Create a Docker Hub access token.
2. Configure GitHub Actions repository secrets and variables.
3. Create a Docker build-and-push workflow.
4. Automatically trigger workflows on Git pushes.
5. Authenticate GitHub Actions with Docker Hub.
6. Publish Docker images with version tags.
7. Monitor workflow execution.
8. Verify published images in Docker Hub.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧰 Prerequisites

Ensure you have:

- Completed Lab 03006 — GitHub Repository, GitHub Actions & AWS OIDC Authentication.
- GitHub repository `vyomanant-web`.
- Docker Hub repository `vyomanant-web`.
- Application files `index.html` and `Dockerfile`.
- Git installed and configured.
- Permission to manage GitHub repository secrets and variables.
- Permission to create Docker Hub access tokens.

### 🔎 Verify Git Repository

Navigate to your application directory:

```
cd vyomanant-web
```

```
git status
```

✅ Expected: You are on the `main` branch, with the application files available.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🏗️ Lab Architecture

```
                  Developer
                      │
                      │ git push
                      ▼
          ┌─────────────────────────┐
          │ GitHub Repository       │
          │ vyomanant-web           │
          │                         │
          │ index.html              │
          │ Dockerfile              │
          └────────────┬────────────┘
                       │
                       ▼
          ┌─────────────────────────┐
          │ GitHub Actions          │
          │ dockerhub-ci.yml        │
          │                         │
          │ 1. Checkout Code        │
          │ 2. Login to Docker Hub  │
          │ 3. Build Docker Image   │
          │ 4. Push Image           │
          └────────────┬────────────┘
                       │
                       │ Docker Hub Token
                       ▼
          ┌─────────────────────────┐
          │ Docker Hub              │
          │                         │
          │ username/vyomanant-web  │
          │                         │
          │ Tags: v2, latest        │
          └─────────────────────────┘
```

Architecture explanation:

- A Git push triggers GitHub Actions.
- GitHub Actions uses a hosted runner to build the Docker image.
- Repository secrets provide Docker Hub authentication.
- The workflow publishes the image using the tags `v2` and `latest`.

Note: Amazon EKS is not modified in this lab.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🚀 Step-by-Step Instructions

## 🔹 Step 1 — Create a Docker Hub Access Token

🎯 Objective: Create credentials for GitHub Actions to publish images.

1. Open [Docker Hub](https://hub.docker.com/?utm_source=chatgpt.com).
2. Sign in to your account.
3. Open Account Settings → Personal access tokens.
4. Select Generate new token.
5. Configure:

| Setting     | Value                                |
| ----------- | ------------------------------------ |
| Description | `github-actions-vyomanant`           |
| Permissions | Read, Write                          |
| Expiration  | Shortest practical training duration |

6. Generate the token.
7. Copy it immediately.

⚠️ Security Warning: Treat the token like a password. Do not commit it to GitHub or paste it into a workflow file.

## 🔹 Step 2 — Configure GitHub Secrets and Variables

🎯 Objective: Store Docker Hub authentication details securely.

Open your GitHub repository:

Settings → Secrets and variables → Actions

### Add Repository Secrets

Under Secrets, create:

| Secret Name       | Value                        |
| ----------------- | ---------------------------- |
| `DOCKERHUB_TOKEN` | Your Docker Hub access token |

### Add Repository Variable

Under Variables, create:

| Variable Name        | Value                    |
| -------------------- | ------------------------ |
| `DOCKERHUB_USERNAME` | Your Docker Hub username |

🧠 Why It Matters: GitHub Actions reads these values during execution without storing credentials in source code.

## 🔹 Step 3 — Update the Application Version

🎯 Objective: Make a visible application change for the CI pipeline.

Open the HTML file:

```
nano index.html
```

Update the application version from:

```
<p>Application Version: v1</p>
```

To:

```
<p>Application Version: v2</p>
```

Save the file.

Important: This change allows us to identify the new image when it is deployed in a later lab.

## 🔹 Step 4 — Create the GitHub Actions Workflow

🎯 Objective: Automate Docker image building and publishing.

The workflow directory already exists from Lab 03006.

Create the workflow file:

```
nano .github/workflows/dockerhub-ci.yml
```

Add:

```
name: Docker Hub CI Pipeline

on:
  push:
    branches:
      - main
    paths:
      - Dockerfile
      - index.html
      - .github/workflows/dockerhub-ci.yml

  workflow_dispatch:

permissions:
  contents: read

jobs:
  docker-build-push:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Repository
        uses: actions/checkout@v4

      - name: Login to Docker Hub
        uses: docker/login-action@v3
        with:
          username: ${{ vars.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}

      - name: Build and Push Docker Image
        uses: docker/build-push-action@v6
        with:
          context: .
          push: true
          tags: |
            ${{ vars.DOCKERHUB_USERNAME }}/vyomanant-web:v2
            ${{ vars.DOCKERHUB_USERNAME }}/vyomanant-web:latest
```

### Workflow Explanation

| Component                  | Purpose                                                        |
| -------------------------- | -------------------------------------------------------------- |
| `push`                     | Triggers CI on changes to the `main` branch                    |
| `paths`                    | Limits automatic runs to relevant files                        |
| `workflow_dispatch`        | Enables manual execution                                       |
| `actions/checkout`         | Downloads repository code                                      |
| `docker/login-action`      | Authenticates to Docker Hub                                    |
| `docker/build-push-action` | Builds and pushes the image                                    |
| `v2`                       | Identifies this application version                            |
| `latest`                   | Points to the most recently published image from this workflow |

Technical Note: The `v2` tag is fixed for this training lab. In production, use unique tags such as commit SHAs or release versions to prevent later builds from silently replacing a version.

🔐 Security Note: For production pipelines, pin third-party GitHub Actions to reviewed commit SHAs.

## 🔹 Step 5 — Commit and Push the Changes

🎯 Objective: Trigger the GitHub Actions workflow.

Stage the files:

```
git add index.html .github/workflows/dockerhub-ci.yml
```

Commit:

```
git commit -m "Add Docker Hub CI pipeline and application v2"
```

Push:

```
git push
```

✅ Expected: The push succeeds, and GitHub Actions automatically starts the Docker Hub CI workflow.

## 🔹 Step 6 — Monitor the GitHub Actions Workflow

🎯 Objective: Verify that the automated build completes.

1. Open your GitHub repository.
2. Select Actions.
3. Open Docker Hub CI Pipeline.
4. Select the latest workflow run.
5. Open the `docker-build-push` job.

### Expected Workflow Stages

```
Checkout Repository
        │
        ▼
Login to Docker Hub
        │
        ▼
Build and Push Docker Image
        │
        ▼
Workflow Completed
```

✅ Expected: All workflow steps complete successfully.

🔎 Observation: GitHub Actions built and published the Docker image without requiring local Docker commands.

## 🔹 Step 7 — Verify the Images in Docker Hub

🎯 Objective: Confirm that the CI pipeline published both image tags.

1. Open Docker Hub.
2. Navigate to Repositories → vyomanant-web.
3. Open the Tags section.

### Expected Tags

| Tag      | Purpose                                 |
| -------- | --------------------------------------- |
| `v1`     | Original image from Lab 03002           |
| `v2`     | New application version                 |
| `latest` | Latest image published by this workflow |

✅ Expected: Both `v2` and `latest` are available.

### Image Names

```
YOUR_DOCKERHUB_USERNAME/vyomanant-web:v2
YOUR_DOCKERHUB_USERNAME/vyomanant-web:latest
```

Important: Replace `YOUR_DOCKERHUB_USERNAME` with your Docker Hub username.

## 🔹 Step 8 — Test the Published Image Locally

🎯 Objective: Verify that the Docker Hub image contains application version `v2`.

Pull the image:

```
docker pull YOUR_DOCKERHUB_USERNAME/vyomanant-web:v2
```

Run it:

```
docker run -d --name vyomanant-web-v2 -p 8080:80 YOUR_DOCKERHUB_USERNAME/vyomanant-web:v2
```

Test:

```
curl http://localhost:8080
```

✅ Expected: The response contains:

```
Welcome to Vyomanant Academy
Docker + Kubernetes + AWS DevOps
Application Version: v2
Container CI/CD Integration Lab
```

Note: Port `8080` must be available. The local container from Lab 03002 should already have been removed.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🔎 Verification

| Check                                | Expected Result    |
| ------------------------------------ | ------------------ |
| Docker Hub access token              | Created            |
| GitHub secret `DOCKERHUB_TOKEN`      | Configured         |
| GitHub variable `DOCKERHUB_USERNAME` | Configured         |
| Workflow file                        | `dockerhub-ci.yml` |
| Trigger                              | Push to `main`     |
| Docker image build                   | Successful         |
| Docker Hub authentication            | Successful         |
| Image tag `v2`                       | Published          |
| Image tag `latest`                   | Published          |
| Local application test               | Displays `v2`      |
| GitHub Actions workflow              | Successful         |

Lab completion criteria: A Git push automatically builds and publishes the Docker image to Docker Hub, and the resulting `v2` image runs successfully.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🛠️ Troubleshooting

| Issue                      | Possible Cause                                       | Resolution                                        |
| -------------------------- | ---------------------------------------------------- | ------------------------------------------------- |
| Workflow does not start    | Incorrect branch or path filter                      | Verify the changed files and workflow trigger     |
| Docker Hub login fails     | Invalid or expired token                             | Generate a new token and update the GitHub secret |
| Image push denied          | Incorrect username or repository permissions         | Verify repository ownership and token permissions |
| Docker build fails         | Invalid Dockerfile or missing application file       | Review build logs and source files                |
| Port `8080` already in use | Another local container or service is using the port | Stop the conflicting lab container                |

### 🔎 Check Local Containers

```
docker ps -a
```

### 🔎 Check Application Logs

```
docker logs vyomanant-web-v2
```

Important: Review the failed step in GitHub Actions before changing the workflow.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧹 Cleanup

🎯 Objective: Remove only the temporary local test container.

Stop the container:

```
docker stop vyomanant-web-v2
```

Remove it:

```
docker rm vyomanant-web-v2
```

Verify:

```
docker ps -a
```

✅ Expected: The `vyomanant-web-v2` container is no longer listed.

### Resources to Keep

| Resource                           | Action |
| ---------------------------------- | ------ |
| GitHub repository `vyomanant-web`  | Keep   |
| Docker Hub workflow                | Keep   |
| Docker Hub image `v1`              | Keep   |
| Docker Hub image `v2`              | Keep   |
| Docker Hub image `latest`          | Keep   |
| GitHub secret and variable         | Keep   |
| Amazon ECR repository `myapp`      | Keep   |
| Amazon EKS cluster `vyomanant-eks` | Keep   |

⚠️ Security Reminder: Revoke the Docker Hub access token when the training project is permanently retired.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧠 Key Takeaways

1. GitHub Actions can automate Docker image builds.
2. A Git push can trigger a CI workflow automatically.
3. Docker Hub access tokens allow automated registry authentication.
4. GitHub secrets protect sensitive credentials.
5. Repository variables store non-sensitive configuration.
6. Docker build-and-push actions simplify CI pipelines.
7. Image tags identify application versions.
8. Automated builds reduce manual work and improve repeatability.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 📚 Official Documentation

- [GitHub Actions Documentation](https://docs.github.com/en/actions?utm_source=chatgpt.com)
- [GitHub Actions Workflow Syntax](https://docs.github.com/en/actions/reference/workflows-and-actions/workflow-syntax?utm_source=chatgpt.com)
- [GitHub Actions Secrets](https://docs.github.com/en/actions/security-for-github-actions/security-guides/using-secrets-in-github-actions?utm_source=chatgpt.com)
- [Docker Hub Access Tokens](https://docs.docker.com/security/for-developers/access-tokens/?utm_source=chatgpt.com)
- [Docker Login GitHub Action](https://github.com/docker/login-action?utm_source=chatgpt.com)
- [Docker Build and Push GitHub Action](https://github.com/docker/build-push-action?utm_source=chatgpt.com)
- [Docker Hub Repositories](https://docs.docker.com/docker-hub/repos/?utm_source=chatgpt.com)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Next Lab: 03008 — Automate Docker Build & Push to Amazon ECR Using GitHub Actions

We will extend our CI/CD automation to AWS by using GitHub OIDC authentication, building the Docker image, and publishing it to the private Amazon ECR repository without storing AWS access keys.
