# 🧪 Lab 03009 — Automate Amazon EKS Deployment & Rolling Updates Using GitHub Actions

Configure secure Kubernetes access for GitHub Actions and automatically deploy an Amazon ECR container image to EKS using a CI/CD workflow.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🌍 Lab Overview

In Lab 03008, GitHub Actions successfully built and pushed our application image to Amazon ECR.

Now we will automate the deployment process on Amazon EKS.

We will grant the existing GitHub Actions IAM role limited Kubernetes access, create a deployment workflow, and perform a rolling update using the `web-v2` image.

| Property     | Details                                                  |
| ------------ | -------------------------------------------------------- |
| Lab ID       | 03009                                                    |
| Module       | 03 — Container CI/CD Integration                         |
| Difficulty   | Intermediate                                             |
| Duration     | 25–30 minutes                                            |
| Tools        | GitHub Actions, Amazon EKS, Amazon ECR, AWS IAM, kubectl |
| AWS Region   | `ap-south-1`                                             |
| EKS Cluster  | `vyomanant-eks`                                          |
| Namespace    | `vyomanant-lab`                                          |
| Deployment   | `vyomanant-web`                                          |
| IAM Role     | `GitHubActionsECRRole`                                   |
| Source Image | `myapp:web-v2`                                           |
| Workflow     | `eks-deploy.yml`                                         |
| Trigger      | Manual GitHub Actions execution                          |

🎯 Real-World Scenario: A DevOps team needs GitHub Actions to deploy a new application version to Kubernetes without giving the CI/CD pipeline unrestricted cluster administrator permissions.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🎯 Learning Objectives

By the end of this lab, you will be able to:

1. Explain the difference between AWS IAM and Kubernetes RBAC.
2. Grant an IAM role access to an EKS cluster.
3. Configure namespace-scoped Kubernetes permissions.
4. Create a GitHub Actions deployment workflow.
5. Authenticate GitHub Actions to EKS using OIDC.
6. Update a Kubernetes Deployment automatically.
7. Monitor a Kubernetes rolling update.
8. Verify the deployed application version.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧰 Prerequisites

Ensure you have:

- Completed Labs 03001–03008.
- An active EKS cluster named `vyomanant-eks`.
- Namespace `vyomanant-lab`.
- Existing Deployment `vyomanant-web`.
- Existing ClusterIP Service `vyomanant-web-service`.
- ECR repository `myapp` containing `web-v2`.
- IAM role `GitHubActionsECRRole`.
- GitHub variable `AWS_ROLE_ARN`.
- AWS CLI, kubectl, and Git installed.
- EKS cluster administrator access to configure access entries and RBAC.

### 🔎 Verify AWS Authentication

```
export AWS_PROFILE=vyomanant
```

```
aws sts get-caller-identity
```

### 🔎 Verify EKS

```
kubectl get nodes
```

```
kubectl get deployment vyomanant-web -n vyomanant-lab
```

✅ Expected: Worker nodes are `Ready`, and the existing application Deployment is available.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🏗️ Lab Architecture

```
                 GitHub Repository
                 vyomanant-web
                        │
                        ▼
              ┌──────────────────┐
              │ GitHub Actions   │
              │ eks-deploy.yml   │
              └────────┬─────────┘
                       │
                       │ OIDC Authentication
                       ▼
              ┌──────────────────┐
              │ AWS IAM + STS    │
              │ GitHubActions   │
              │ ECRRole         │
              └────────┬─────────┘
                       │
                       │ EKS Access Entry
                       ▼
┌─────────────────────────────────────────────┐
│ Amazon EKS: vyomanant-eks                   │
│                                             │
│ Namespace: vyomanant-lab                    │
│                                             │
│ Kubernetes RBAC                             │
│ Role + RoleBinding                          │
│           │                                 │
│           ▼                                 │
│ Deployment: vyomanant-web                   │
│           │                                 │
│           │ Rolling Update                  │
│           ▼                                 │
│    ┌────────────┐  ┌────────────┐           │
│    │ Web Pod 1  │  │ Web Pod 2  │           │
│    │ web-v2     │  │ web-v2     │           │
│    └──────┬─────┘  └─────┬──────┘           │
│           └──────┬───────┘                  │
│                  ▼                          │
│       ClusterIP Service                     │
│       vyomanant-web-service                 │
└─────────────────────────────────────────────┘
                   ▲
                   │ Image Pull
                   │
            Amazon ECR: myapp
            Image: web-v2
```

Architecture explanation:

- GitHub Actions authenticates to AWS through OIDC.
- An EKS access entry recognizes the IAM role.
- Kubernetes RBAC limits the role to managing the designated Deployment.
- The workflow updates the Deployment to use `myapp:web-v2`.
- EKS worker nodes pull the image from Amazon ECR.

Important: AWS authentication and Kubernetes authorization are separate security controls.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🚀 Step-by-Step Instructions

## 🔹 Step 1 — Verify the ECR Image

🎯 Objective: Confirm that the new application image is ready for deployment.

```
aws ecr describe-images --repository-name myapp --image-ids imageTag=web-v2 --region ap-south-1
```

✅ Expected: AWS returns image details for `web-v2`.

Record your ECR image URI from AWS Console → ECR → Repositories → myapp → Images.

Example:

```
123456789012.dkr.ecr.ap-south-1.amazonaws.com/myapp:web-v2
```

Replace `123456789012` with your actual AWS account ID.

## 🔹 Step 2 — Grant the GitHub Actions Role EKS Access

🎯 Objective: Allow EKS to authenticate the existing GitHub Actions IAM role.

### AWS Console Steps

1. Open Amazon EKS → Clusters → vyomanant-eks.
2. Select Access.
3. Check the cluster's authentication mode.
4. Ensure it supports EKS access entries (`API` or `API_AND_CONFIG_MAP`).
5. Select Create access entry.
6. Choose the IAM role `GitHubActionsECRRole`.
7. Select access entry type Standard.
8. Do not associate an EKS administrator access policy.
9. Set the Kubernetes group to:

```
github-actions-deployer
```

10\. Create the access entry.

⚠️ Important: If the cluster uses `CONFIG_MAP` authentication only, an administrator must enable access entries before proceeding. Changing the authentication mode is a cluster-level security change and should be approved.

🔐 Security Note: The access entry authenticates the IAM role and associates it with a Kubernetes group. Kubernetes RBAC will define what that group can do.

## 🔹 Step 3 — Create Namespace-Scoped Kubernetes RBAC

🎯 Objective: Allow GitHub Actions to update only the intended Deployment.

Navigate to your existing Kubernetes manifest directory:

```
cd vyomanant-eks-app
```

Create:

```
nano github-actions-rbac.yaml
```

Add:

```
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: github-actions-deployer
  namespace: vyomanant-lab
rules:
  - apiGroups: ["apps"]
    resources: ["deployments"]
    resourceNames: ["vyomanant-web"]
    verbs: ["get", "patch"]
---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: github-actions-deployer-binding
  namespace: vyomanant-lab
subjects:
  - kind: Group
    name: github-actions-deployer
    apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: github-actions-deployer
  apiGroup: rbac.authorization.k8s.io
```

Apply:

```
kubectl apply -f github-actions-rbac.yaml
```

Verify:

```
kubectl get role,rolebinding -n vyomanant-lab
```

✅ Expected: The Role and RoleBinding are created.

### RBAC Permissions

| Resource                   | Allowed Operations |
| -------------------------- | ------------------ |
| Deployment `vyomanant-web` | Get, Patch         |
| Other Deployments          | Not granted        |
| Pods                       | Not granted        |
| Secrets                    | Not granted        |
| Other namespaces           | Not granted        |

🧠 Why It Matters: The workflow can update the intended application without receiving unrestricted Kubernetes access.

## 🔹 Step 4 — Grant AWS Permission to Discover the EKS Cluster

🎯 Objective: Allow GitHub Actions to retrieve the EKS cluster endpoint and certificate information.

Open AWS Console → IAM → Roles → GitHubActionsECRRole.

Create an inline policy named:

```
GitHubActionsEKSDescribePolicy
```

Use:

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "eks:DescribeCluster",
      "Resource": "arn:aws:eks:ap-south-1:123456789012:cluster/vyomanant-eks"
    }
  ]
}
```

Replace the example account ID.

Important: `eks:DescribeCluster` does not grant Kubernetes administrator permissions. The Kubernetes Role and RoleBinding from Step 3 control Deployment updates.

## 🔹 Step 5 — Create the GitHub Actions EKS Deployment Workflow

🎯 Objective: Automate deployment of the ECR image to EKS.

Navigate to your GitHub application directory:

```
cd vyomanant-web
```

Create the workflow:

```
nano .github/workflows/eks-deploy.yml
```

Add:

```
name: Amazon EKS Deployment

on:
  workflow_dispatch:

permissions:
  id-token: write
  contents: read

jobs:
  deploy-to-eks:
    runs-on: ubuntu-latest

    steps:
      - name: Configure AWS Credentials
        uses: aws-actions/configure-aws-credentials@v4
        with:
          role-to-assume: ${{ vars.AWS_ROLE_ARN }}
          aws-region: ap-south-1

      - name: Configure Kubernetes Access
        run: |
          aws eks update-kubeconfig \
            --region ap-south-1 \
            --name vyomanant-eks

      - name: Update Application Image
        run: |
          kubectl set image deployment/vyomanant-web \
            vyomanant-web=${{ steps.image.outputs.uri }} \
            -n vyomanant-lab
```

⚠️ Technical Note: The workflow above references `steps.image.outputs.uri`, but no step named `image` exists yet. Add the following step before `Update Application Image`:

```
      - name: Resolve ECR Image
        id: image
        run: |
          ACCOUNT_ID=$(aws sts get-caller-identity --query Account --output text)
          echo "uri=$ACCOUNT_ID.dkr.ecr.ap-south-1.amazonaws.com/myapp:web-v2" >> "$GITHUB_OUTPUT"
```

Then add this step after `Update Application Image`:

```
      - name: Verify Deployment Rollout
        run: |
          kubectl rollout status deployment/vyomanant-web \
            -n vyomanant-lab \
            --timeout=180s
```

Important: The workflow must contain all five steps in this order: Configure AWS Credentials, Configure Kubernetes Access, Resolve ECR Image, Update Application Image, and Verify Deployment Rollout.

### Workflow Explanation

| Component                | Purpose                              |
| ------------------------ | ------------------------------------ |
| `workflow_dispatch`      | Manual deployment trigger            |
| AWS OIDC                 | Temporary AWS credentials            |
| `update-kubeconfig`      | Configures Kubernetes cluster access |
| Resolve ECR Image        | Builds the full ECR image URI        |
| `kubectl set image`      | Updates the Deployment image         |
| `kubectl rollout status` | Waits for successful rollout         |

🔐 Security Note: Production workflows should pin third-party GitHub Actions to reviewed commit SHAs.

## 🔹 Step 6 — Commit and Push the Workflow

🎯 Objective: Publish the deployment automation to GitHub.

```
git add .github/workflows/eks-deploy.yml
```

```
git commit -m "Add Amazon EKS deployment workflow"
```

```
git push
```

✅ Expected: The deployment workflow appears in the GitHub repository.

## 🔹 Step 7 — Run the EKS Deployment Workflow

🎯 Objective: Deploy the ECR image automatically.

### GitHub Steps

1. Open GitHub → vyomanant-web → Actions.
2. Select Amazon EKS Deployment.
3. Select Run workflow.
4. Choose branch `main`.
5. Start the workflow.
6. Open the workflow run and inspect its logs.

### Expected Execution

```
Configure AWS Credentials
          │
          ▼
Configure Kubernetes Access
          │
          ▼
Resolve ECR Image
          │
          ▼
Update Application Image
          │
          ▼
Verify Deployment Rollout
          │
          ▼
Deployment Successful
```

✅ Expected:

```
deployment.apps/vyomanant-web image updated
deployment "vyomanant-web" successfully rolled out
```

Note: The GitHub-hosted runner must have network access to the EKS Kubernetes API endpoint. The training cluster must permit that connectivity.

## 🔹 Step 8 — Verify the EKS Rolling Update

🎯 Objective: Confirm that EKS is running the new image.

```
kubectl get deployment vyomanant-web -n vyomanant-lab
```

Expected: The Deployment shows `2/2` ready replicas.

Inspect the Deployment:

```
kubectl describe deployment vyomanant-web -n vyomanant-lab
```

Expected image:

```
123456789012.dkr.ecr.ap-south-1.amazonaws.com/myapp:web-v2
```

Check Pods:

```
kubectl get pods -n vyomanant-lab
```

✅ Expected: Two application Pods are running.

### Test the Application

```
kubectl port-forward service/vyomanant-web-service 8080:80 -n vyomanant-lab
```

Open another terminal:

```
curl http://localhost:8080
```

Expected:

```
Welcome to Vyomanant Academy
Docker + Kubernetes + AWS DevOps
Application Version: v2
Container CI/CD Integration Lab
```

Stop port forwarding using Ctrl+C.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🔎 Verification

| Check                      | Expected Result           |
| -------------------------- | ------------------------- |
| ECR image                  | `myapp:web-v2`            |
| EKS access entry           | Created                   |
| Kubernetes group           | `github-actions-deployer` |
| Kubernetes Role            | Created                   |
| Kubernetes RoleBinding     | Created                   |
| IAM `eks:DescribeCluster`  | Granted                   |
| GitHub deployment workflow | Created                   |
| GitHub Actions run         | Successful                |
| Deployment image           | `myapp:web-v2`            |
| Deployment replicas        | 2/2 Ready                 |
| Application version        | `v2`                      |

Lab completion criteria: GitHub Actions successfully authenticates to AWS, accesses EKS using restricted permissions, updates the application image, and verifies the rolling deployment.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🛠️ Troubleshooting

| Issue                          | Possible Cause                           | Resolution                             |
| ------------------------------ | ---------------------------------------- | -------------------------------------- |
| `AccessDeniedException`        | Missing `eks:DescribeCluster` permission | Verify IAM policy                      |
| `Unauthorized` from Kubernetes | EKS access entry missing or incorrect    | Verify IAM role and Kubernetes group   |
| `Forbidden` from Kubernetes    | Role or RoleBinding missing permissions  | Inspect namespace RBAC                 |
| EKS API timeout                | Runner cannot reach the cluster endpoint | Review EKS endpoint access settings    |
| Rollout timeout                | New Pods cannot pull or start the image  | Inspect Pod events and ECR permissions |

### 🔎 Inspect Kubernetes Events

```
kubectl get events -n vyomanant-lab
```

### 🔎 Inspect Deployment

```
kubectl describe deployment vyomanant-web -n vyomanant-lab
```

Important: Do not grant `cluster-admin` permissions merely to resolve a deployment authorization error.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧹 Cleanup

Keep the resources for Lab 03010 — Complete CI/CD Capstone.

| Resource                          | Action |
| --------------------------------- | ------ |
| GitHub repository `vyomanant-web` | Keep   |
| GitHub workflow `eks-deploy.yml`  | Keep   |
| IAM role `GitHubActionsECRRole`   | Keep   |
| EKS access entry                  | Keep   |
| Kubernetes Role                   | Keep   |
| Kubernetes RoleBinding            | Keep   |
| ECR image `web-v2`                | Keep   |
| EKS Deployment                    | Keep   |
| EKS Service                       | Keep   |

No cleanup is required now.

⚠️ Cost Reminder: EKS control plane, EC2 worker nodes, and ECR image storage may continue generating AWS charges.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧠 Key Takeaways

1. GitHub Actions can automate EKS application deployments.
2. GitHub OIDC enables secure AWS authentication.
3. EKS access entries connect AWS IAM identities to Kubernetes.
4. Kubernetes RBAC restricts deployment permissions.
5. IAM and Kubernetes RBAC serve different authorization purposes.
6. `kubectl set image` updates a running Deployment.
7. Kubernetes rolling updates replace application Pods gradually.
8. GitHub Actions can verify deployment success using rollout status.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 📚 Official Documentation

- [Amazon EKS Access Entries](https://docs.aws.amazon.com/eks/latest/userguide/access-entries.html?utm_source=chatgpt.com)
- [Amazon EKS Authentication Modes](https://docs.aws.amazon.com/eks/latest/userguide/grant-k8s-access.html?utm_source=chatgpt.com)
- [Kubernetes RBAC](https://kubernetes.io/docs/reference/access-authn-authz/rbac/?utm_source=chatgpt.com)
- [AWS CLI Update Kubeconfig](https://docs.aws.amazon.com/cli/latest/reference/eks/update-kubeconfig.html?utm_source=chatgpt.com)
- [kubectl Set Image](https://kubernetes.io/docs/reference/kubectl/generated/kubectl_set/kubectl_set_image/?utm_source=chatgpt.com)
- [Kubernetes Rolling Updates](https://kubernetes.io/docs/tutorials/kubernetes-basics/update/update-intro/?utm_source=chatgpt.com)
- [GitHub OIDC Authentication with AWS](https://docs.github.com/en/actions/how-tos/secure-your-work/security-harden-deployments/oidc-in-aws?utm_source=chatgpt.com)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Next Lab: 03010 — Capstone: Complete CI/CD Pipeline Using GitHub Actions, Docker Hub, Amazon ECR & Amazon EKS

We will connect application changes, automated Docker image builds, Docker Hub and ECR publishing, and Amazon EKS deployment into one complete end-to-end CI/CD pipeline.
