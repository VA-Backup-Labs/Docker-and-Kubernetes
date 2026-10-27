# 🧪 Lab 03010 — Capstone: Complete CI/CD Pipeline Using GitHub Actions, Docker Hub, Amazon ECR & EKS

Build a complete end-to-end CI/CD pipeline that automatically builds, publishes, and deploys the Vyomanant Academy application to Amazon EKS whenever code changes are pushed to GitHub.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🌍 Lab Overview

In the previous labs, we configured Docker Hub, Amazon ECR, Amazon EKS, GitHub Actions, AWS OIDC authentication, and Kubernetes RBAC.

In this final capstone, we will connect everything into one automated CI/CD pipeline.

A single Git push will trigger the following process:

1. Build a Docker image.
2. Push the image to Docker Hub.
3. Push the same image to Amazon ECR.
4. Deploy the ECR image to Amazon EKS.
5. Verify the Kubernetes rolling update.

| Property              | Details                                                     |
| --------------------- | ----------------------------------------------------------- |
| Lab ID                | 03010                                                       |
| Module                | 03 — Container CI/CD Integration                            |
| Difficulty            | Intermediate                                                |
| Duration              | 30–40 minutes                                               |
| Tools                 | GitHub Actions, Docker Hub, Amazon ECR, Amazon EKS, kubectl |
| GitHub Repository     | `vyomanant-web`                                             |
| Docker Hub Repository | `vyomanant-web`                                             |
| ECR Repository        | `myapp`                                                     |
| EKS Cluster           | `vyomanant-eks`                                             |
| Namespace             | `vyomanant-lab`                                             |
| Deployment            | `vyomanant-web`                                             |
| Workflow              | `complete-cicd.yml`                                         |
| Trigger               | Push to `main`                                              |
| Image Version         | Unique Git commit SHA                                       |

🎯 Real-World Scenario: A DevOps team wants developers to focus on application code while GitHub Actions automatically handles container image publishing and Kubernetes deployment.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🎯 Learning Objectives

By the end of this lab, you will be able to:

1. Design an end-to-end container CI/CD pipeline.
2. Automatically trigger builds using GitHub Actions.
3. Build a Docker image from application source code.
4. Publish the same image to Docker Hub and Amazon ECR.
5. Use unique image tags for reliable deployments.
6. Authenticate to AWS using GitHub OIDC.
7. Deploy applications automatically to Amazon EKS.
8. Verify rolling updates and application availability.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧰 Prerequisites

Ensure you have completed Labs 03001–03009.

### Required Resources

| Resource              | Expected Configuration  |
| --------------------- | ----------------------- |
| GitHub repository     | `vyomanant-web`         |
| Docker Hub repository | `vyomanant-web`         |
| GitHub secret         | `DOCKERHUB_TOKEN`       |
| GitHub variable       | `DOCKERHUB_USERNAME`    |
| GitHub variable       | `AWS_ROLE_ARN`          |
| IAM role              | `GitHubActionsECRRole`  |
| ECR repository        | `myapp`                 |
| EKS cluster           | `vyomanant-eks`         |
| Namespace             | `vyomanant-lab`         |
| Deployment            | `vyomanant-web`         |
| Service               | `vyomanant-web-service` |
| Kubernetes RBAC       | Configured in Lab 03009 |

The IAM role must have ECR push permissions and `eks:DescribeCluster` access. Its EKS access entry must map to the `github-actions-deployer` Kubernetes group.

Important: The EKS Kubernetes API endpoint must be reachable from the GitHub Actions runner. This lab assumes the cluster has a publicly reachable endpoint with appropriate access restrictions. Do not expose a private production cluster solely to run this lab.

### 🔎 Verify the Existing Deployment

```
kubectl get deployment vyomanant-web -n vyomanant-lab
```

✅ Expected: The Deployment is available with two ready replicas.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🏗️ Lab Architecture

```
                    Developer
                        │
                        │ git push
                        ▼
             ┌──────────────────────┐
             │ GitHub Repository    │
             │ vyomanant-web        │
             └──────────┬───────────┘
                        │
                        ▼
             ┌──────────────────────┐
             │ GitHub Actions       │
             │ complete-cicd.yml    │
             │                      │
             │ 1. Checkout Code     │
             │ 2. Build Image       │
             │ 3. Push Docker Hub   │
             │ 4. Push Amazon ECR   │
             │ 5. Deploy to EKS     │
             │ 6. Verify Rollout    │
             └───────┬───────┬──────┘
                     │       │
          ┌──────────┘       └───────────┐
          ▼                              ▼
┌───────────────────┐       ┌────────────────────┐
│ Docker Hub        │       │ Amazon ECR         │
│                   │       │                    │
│ vyomanant-web     │       │ Repository: myapp  │
│ Tag: Git SHA      │       │ Tag: Git SHA       │
└───────────────────┘       └──────────┬─────────┘
                                      │
                                      │ Image Pull
                                      ▼
                           ┌────────────────────┐
                           │ Amazon EKS         │
                           │ vyomanant-eks      │
                           │                    │
                           │ Namespace:         │
                           │ vyomanant-lab      │
                           │                    │
                           │ Deployment:        │
                           │ vyomanant-web      │
                           │                    │
                           │ Pods: 2            │
                           │ Service: ClusterIP │
                           └────────────────────┘
```

Architecture explanation:

- GitHub Actions builds one Docker image and tags it with the Git commit SHA.
- The image is published to Docker Hub and Amazon ECR.
- GitHub OIDC provides temporary AWS credentials.
- The workflow updates the existing EKS Deployment to use the ECR image.
- Kubernetes performs a rolling update and reports deployment status.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🚀 Step-by-Step Instructions

## 🔹 Step 1 — Verify GitHub Credentials and AWS Access

🎯 Objective: Confirm that the required authentication settings are available.

Open your GitHub repository:

Settings → Secrets and variables → Actions

Verify:

| Type     | Name                 |
| -------- | -------------------- |
| Secret   | `DOCKERHUB_TOKEN`    |
| Variable | `DOCKERHUB_USERNAME` |
| Variable | `AWS_ROLE_ARN`       |

### Verify AWS IAM

In AWS Console → IAM → Roles, open:

```
GitHubActionsECRRole
```

Confirm the role has:

- ECR authentication and image push permissions for `myapp`.
- `eks:DescribeCluster` permission for `vyomanant-eks`.
- An OIDC trust relationship restricted to the GitHub repository's `main` branch.

🔐 Security: Do not store permanent AWS access keys in GitHub.

## 🔹 Step 2 — Verify EKS Deployment Permissions

🎯 Objective: Confirm that GitHub Actions has Kubernetes access.

```
kubectl get role github-actions-deployer -n vyomanant-lab
```

```
kubectl get rolebinding github-actions-deployer-binding -n vyomanant-lab
```

✅ Expected: Both resources exist.

The Role must allow the GitHub Actions group to `get` and `patch` the `vyomanant-web` Deployment.

Important: The IAM role must also have an EKS access entry mapped to the Kubernetes group `github-actions-deployer`.

## 🔹 Step 3 — Update the Application to Version v3

🎯 Objective: Make a visible application change to test the complete pipeline.

Navigate to your application directory:

```
cd vyomanant-web
```

Open the HTML file:

```
nano index.html
```

Change:

```
<p>Application Version: v2</p>
```

To:

```
<p>Application Version: v3</p>
```

Save the file.

✅ Expected: The application source now displays version `v3`.

## 🔹 Step 4 — Create the Complete CI/CD Workflow

🎯 Objective: Automate image publishing and Kubernetes deployment.

Create the workflow file:

```
nano .github/workflows/complete-cicd.yml
```

Add the following complete workflow:

```
name: Complete Docker ECR EKS CI/CD Pipeline

on:
  push:
    branches:
      - main
    paths:
      - Dockerfile
      - index.html
      - .github/workflows/complete-cicd.yml

  workflow_dispatch:

permissions:
  id-token: write
  contents: read

concurrency:
  group: vyomanant-eks-deployment
  cancel-in-progress: false

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest

    env:
      AWS_REGION: ap-south-1
      ECR_REPOSITORY: myapp
      EKS_CLUSTER: vyomanant-eks
      K8S_NAMESPACE: vyomanant-lab
      IMAGE_TAG: ${{ github.sha }}

    steps:
      - name: Checkout Repository
        uses: actions/checkout@v4

      - name: Configure AWS Credentials
        uses: aws-actions/configure-aws-credentials@v4
        with:
          role-to-assume: ${{ vars.AWS_ROLE_ARN }}
          aws-region: ap-south-1

      - name: Login to Docker Hub
        uses: docker/login-action@v3
        with:
          username: ${{ vars.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}

      - name: Login to Amazon ECR
        id: login-ecr
        uses: aws-actions/amazon-ecr-login@v2

      - name: Build Docker Image
        env:
          ECR_REGISTRY: ${{ steps.login-ecr.outputs.registry }}
          DOCKERHUB_USERNAME: ${{ vars.DOCKERHUB_USERNAME }}
        run: |
          docker build -t $DOCKERHUB_USERNAME/vyomanant-web:$IMAGE_TAG .
          docker tag $DOCKERHUB_USERNAME/vyomanant-web:$IMAGE_TAG $ECR_REGISTRY/$ECR_REPOSITORY:$IMAGE_TAG

      - name: Push Image to Docker Hub
        env:
          DOCKERHUB_USERNAME: ${{ vars.DOCKERHUB_USERNAME }}
        run: |
          docker push $DOCKERHUB_USERNAME/vyomanant-web:$IMAGE_TAG

      - name: Push Image to Amazon ECR
        env:
          ECR_REGISTRY: ${{ steps.login-ecr.outputs.registry }}
        run: |
          docker push $ECR_REGISTRY/$ECR_REPOSITORY:$IMAGE_TAG

      - name: Configure Kubernetes Access
        run: |
          aws eks update-kubeconfig --region $AWS_REGION --name $EKS_CLUSTER

      - name: Deploy to Amazon EKS
        env:
          ECR_REGISTRY: ${{ steps.login-ecr.outputs.registry }}
        run: |
          kubectl set image deployment/vyomanant-web vyomanant-web=$ECR_REGISTRY/$ECR_REPOSITORY:$IMAGE_TAG -n $K8S_NAMESPACE

      - name: Verify Deployment
        run: |
          kubectl rollout status deployment/vyomanant-web -n $K8S_NAMESPACE --timeout=180s
```

### Workflow Explanation

| Stage            | Action                                      |
| ---------------- | ------------------------------------------- |
| Checkout         | Download application source                 |
| AWS OIDC         | Authenticate using temporary credentials    |
| Docker Hub login | Authenticate using access token             |
| ECR login        | Authenticate to private AWS registry        |
| Build            | Create one Docker image                     |
| Tag              | Reference the same image in both registries |
| Push Docker Hub  | Publish image with Git SHA tag              |
| Push ECR         | Publish image with the same Git SHA tag     |
| Configure EKS    | Generate Kubernetes configuration           |
| Deploy           | Update the Deployment image                 |
| Verify           | Wait for successful rollout                 |

### Why Use Git Commit SHA Tags?

Example:

```
Docker Hub:
username/vyomanant-web:abc123...

Amazon ECR:
123456789012.dkr.ecr.ap-south-1.amazonaws.com/myapp:abc123...
```

Both registries receive the same image content.

The full Git commit SHA provides a unique image tag for each source revision, avoiding collisions with existing version tags in the immutable ECR repository.

Technical Note: Re-running the workflow for an already-published commit can fail because the ECR repository enforces tag immutability. For this capstone, use a new commit for each deployment test.

🔐 Production Best Practice: Pin third-party GitHub Actions to reviewed commit SHAs and introduce automated testing and deployment approvals before using this workflow for production releases.

## 🔹 Step 5 — Prevent Duplicate CI Pipelines

🎯 Objective: Avoid running three independent image publishing workflows for the same application change.

Previous labs created:

```
.github/workflows/
├── aws-auth-test.yml
├── dockerhub-ci.yml
├── ecr-ci.yml
└── complete-cicd.yml
```

The Docker Hub and ECR workflows from Labs 03007 and 03008 also react to application changes.

Disable their automatic execution by changing their triggers to manual-only.

Open:

```
nano .github/workflows/dockerhub-ci.yml
```

Replace its existing `on` section with:

```
on:
  workflow_dispatch:
```

Repeat for:

```
nano .github/workflows/ecr-ci.yml
```

Use the same manual-only trigger.

Important: Keep the existing jobs and steps unchanged. Modify only the workflow triggers.

✅ Expected: The complete CI/CD workflow is the only pipeline automatically triggered by application changes.

## 🔹 Step 6 — Commit and Push the Application Changes

🎯 Objective: Trigger the complete CI/CD pipeline.

Stage the files:

```
git add index.html .github/workflows/
```

Commit:

```
git commit -m "Add complete CI CD pipeline and application v3"
```

Push:

```
git push
```

✅ Expected: GitHub receives the changes and starts the complete CI/CD workflow.

## 🔹 Step 7 — Monitor the Complete CI/CD Pipeline

🎯 Objective: Verify the entire automation process.

1. Open GitHub → vyomanant-web → Actions.
2. Select Complete Docker ECR EKS CI/CD Pipeline.
3. Open the latest workflow run.
4. Inspect the `build-and-deploy` job.

### Expected Pipeline Execution

```
Checkout Repository
        │
        ▼
Configure AWS Credentials
        │
        ▼
Login to Docker Hub
        │
        ▼
Login to Amazon ECR
        │
        ▼
Build Docker Image
        │
        ▼
Push Image to Docker Hub
        │
        ▼
Push Image to Amazon ECR
        │
        ▼
Configure Kubernetes Access
        │
        ▼
Deploy to Amazon EKS
        │
        ▼
Verify Deployment
        │
        ▼
CI/CD Pipeline Successful
```

✅ Expected: All workflow steps complete successfully.

## 🔹 Step 8 — Verify Images in Both Registries

🎯 Objective: Confirm that the application image was published to Docker Hub and ECR.

### Docker Hub Verification

1. Open [Docker Hub](https://hub.docker.com/?utm_source=chatgpt.com).
2. Navigate to `vyomanant-web`.
3. Open Tags.
4. Find the tag matching the Git commit SHA.

### Amazon ECR Verification

1. Open AWS Console → Amazon ECR.
2. Select repository `myapp`.
3. Open Images.
4. Find the same Git commit SHA tag.

✅ Expected: Both registries contain the image built by GitHub Actions.

🔎 Observation: The image registry URLs differ, but the source revision and application content are the same.

## 🔹 Step 9 — Verify the EKS Deployment

🎯 Objective: Confirm that Kubernetes is running the new application image.

Check the Deployment:

```
kubectl get deployment vyomanant-web -n vyomanant-lab
```

Expected:

```
NAME            READY   UP-TO-DATE   AVAILABLE
vyomanant-web   2/2     2            2
```

Check the Pods:

```
kubectl get pods -n vyomanant-lab
```

Inspect the Deployment image:

```
kubectl describe deployment vyomanant-web -n vyomanant-lab
```

✅ Expected: The image URI points to Amazon ECR and contains the latest Git commit SHA.

### Access the Application

Start port forwarding:

```
kubectl port-forward service/vyomanant-web-service 8080:80 -n vyomanant-lab
```

In another terminal:

```
curl http://localhost:8080
```

Expected application content:

```
Welcome to Vyomanant Academy
Docker + Kubernetes + AWS DevOps
Application Version: v3
Container CI/CD Integration Lab
```

Stop port forwarding with Ctrl+C.

🎉 Capstone Result: A Git push automatically built, published, and deployed the new application version to Amazon EKS.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🔎 Verification

| Check                     | Expected Result     |
| ------------------------- | ------------------- |
| GitHub repository         | `vyomanant-web`     |
| Application version       | `v3`                |
| Workflow                  | `complete-cicd.yml` |
| Pipeline trigger          | Push to `main`      |
| AWS authentication        | OIDC                |
| Docker Hub authentication | Access token        |
| Docker image build        | Successful          |
| Docker Hub image          | Published           |
| Amazon ECR image          | Published           |
| Image tag                 | Git commit SHA      |
| EKS Deployment            | Updated             |
| Kubernetes replicas       | 2/2 Ready           |
| Application access        | Successful          |
| GitHub Actions workflow   | Successful          |

Lab completion criteria: A single Git push automatically publishes the application image to both registries and deploys it successfully to Amazon EKS.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🛠️ Troubleshooting

| Issue                    | Possible Cause                                         | Resolution                              |
| ------------------------ | ------------------------------------------------------ | --------------------------------------- |
| Docker Hub push fails    | Invalid access token or permissions                    | Verify `DOCKERHUB_TOKEN`                |
| ECR push fails           | Missing IAM permissions or existing immutable tag      | Verify IAM policy and image tag         |
| AWS OIDC fails           | Incorrect role ARN or trust policy                     | Verify `AWS_ROLE_ARN` and GitHub branch |
| EKS deployment forbidden | Missing EKS access entry or Kubernetes RBAC            | Review access entry and RoleBinding     |
| EKS rollout fails        | Image pull, application startup, or connectivity issue | Inspect Deployment and Pod events       |

### 🔎 Check Kubernetes Events

```
kubectl get events -n vyomanant-lab
```

### 🔎 Inspect the Deployment

```
kubectl describe deployment vyomanant-web -n vyomanant-lab
```

### 🔎 Check Application Logs

```
kubectl logs deployment/vyomanant-web -n vyomanant-lab
```

Important: If a deployment fails after the image is published, correct the issue and use a new Git commit before rerunning the complete pipeline against the immutable ECR repository.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧹 Cleanup

This is the final lab in Module 03.

⚠️ AWS Cost Warning: The EKS control plane and EC2 worker nodes continue generating charges until the cluster is deleted.

## 🔹 Step 1 — Remove CI/CD Access

After completing all training activities:

1. Disable or delete the GitHub Actions workflows.
2. Revoke the Docker Hub access token.
3. Remove the EKS access entry for `GitHubActionsECRRole`.
4. Delete the GitHub Actions IAM role and its inline policies.
5. Remove the GitHub OIDC provider only if no other workloads use it.

## 🔹 Step 2 — Remove Application Resources

If you intend to keep the EKS cluster for other training:

```
kubectl delete deployment vyomanant-web -n vyomanant-lab
```

```
kubectl delete service vyomanant-web-service -n vyomanant-lab
```

Verify:

```
kubectl get deployments,services -n vyomanant-lab
```

Important: These commands remove only the capstone application's Deployment and Service. Other lab resources remain.

## 🔹 Step 3 — Delete the EKS Cluster When Training Is Complete

If no further labs require the cluster, use the reusable EKS deletion script prepared in Lab 02009.

Before running it, confirm:

- The cluster is no longer required.
- No other applications depend on it.
- All required application data and configurations have been saved.

Do not delete shared infrastructure without approval.

## 🔹 Step 4 — Review ECR Storage

The `myapp` repository was created in Module 01 and may contain images required by other labs.

Remove unused images only after confirming they are no longer needed.

Note: Deleting an EKS cluster does not automatically delete its ECR repositories or GitHub repositories.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧠 Key Takeaways

1. GitHub Actions automates the complete container CI/CD lifecycle.
2. A single Git push can trigger image builds, registry publishing, and Kubernetes deployment.
3. Docker images can be published to Docker Hub and Amazon ECR without rebuilding them separately.
4. Git commit SHA tags improve image traceability.
5. GitHub OIDC provides temporary AWS credentials without permanent access keys.
6. IAM and Kubernetes RBAC control CI/CD access to EKS.
7. Kubernetes rolling updates deploy new application versions while managing Pod availability.
8. End-to-end CI/CD reduces manual deployment effort and improves repeatability.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 📚 Official Documentation

- [GitHub Actions Documentation](https://docs.github.com/en/actions?utm_source=chatgpt.com)
- [GitHub Actions Workflow Syntax](https://docs.github.com/en/actions/reference/workflows-and-actions/workflow-syntax?utm_source=chatgpt.com)
- [GitHub OIDC Authentication with AWS](https://docs.github.com/en/actions/how-tos/secure-your-work/security-harden-deployments/oidc-in-aws?utm_source=chatgpt.com)
- [Docker Build and Push GitHub Action](https://github.com/docker/build-push-action?utm_source=chatgpt.com)
- [Docker Hub Access Tokens](https://docs.docker.com/security/for-developers/access-tokens/?utm_source=chatgpt.com)
- [Amazon ECR Documentation](https://docs.aws.amazon.com/AmazonECR/latest/userguide/what-is-ecr.html?utm_source=chatgpt.com)
- [Amazon EKS Documentation](https://docs.aws.amazon.com/eks/latest/userguide/what-is-eks.html?utm_source=chatgpt.com)
- [Amazon EKS Access Entries](https://docs.aws.amazon.com/eks/latest/userguide/access-entries.html?utm_source=chatgpt.com)
- [Kubernetes Deployments](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/?utm_source=chatgpt.com)
- [Kubernetes RBAC](https://kubernetes.io/docs/reference/access-authn-authz/rbac/?utm_source=chatgpt.com)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🎓 Module 03 — Completed

Congratulations! You have completed the Vyomanant Academy Container CI/CD Integration Module.

You have implemented the following complete DevOps workflow:

```
Developer
    │
    ▼
GitHub Repository
    │
    ▼
GitHub Actions
    │
    ├──► Docker Build
    │         │
    │         ├──► Docker Hub
    │         │
    │         └──► Amazon ECR
    │                    │
    │                    ▼
    └──────────────► Amazon EKS
                         │
                         ▼
                  Kubernetes Pods
                         │
                         ▼
                  Running Application
```

🏆 Final Outcome: A working CI/CD pipeline integrating GitHub Actions + Docker + Docker Hub + Amazon ECR + Amazon EKS, with secure AWS authentication, versioned container images, and automated Kubernetes rolling updates.
