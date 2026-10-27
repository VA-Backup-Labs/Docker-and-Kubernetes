# 🧪 Lab 03005 — Deploy a Private Amazon ECR Image to EKS & Expose It

Deploy the custom Vyomanant Academy web application from Amazon ECR to Amazon EKS, verify private image access, and expose the application using a Kubernetes Service.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🌍 Lab Overview

In Lab 03004, we pushed our custom Docker image to a private Amazon ECR repository.

Now we will deploy that image to our existing Amazon EKS cluster.

We will reuse the Kubernetes Deployment and ClusterIP Service created in Lab 03003, update the container image from Docker Hub to Amazon ECR, and verify that the application works.

| Property       | Details                                      |
| -------------- | -------------------------------------------- |
| Lab ID         | 03005                                        |
| Module         | 03 — Container CI/CD Integration             |
| Difficulty     | Beginner                                     |
| Duration       | 20–25 minutes                                |
| Tools          | Amazon ECR, Amazon EKS, kubectl, AWS Console |
| Cluster        | `vyomanant-eks`                              |
| Namespace      | `vyomanant-lab`                              |
| Deployment     | `vyomanant-web`                              |
| ECR Repository | `myapp`                                      |
| Image Tag      | `web-v1`                                     |
| Replicas       | 2                                            |
| Service        | `vyomanant-web-service`                      |

🎯 Real-World Scenario: A company stores its application images in a private AWS container registry and deploys them to Amazon EKS without making the images publicly accessible.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🎯 Learning Objectives

By the end of this lab, you will be able to:

1. Identify a private ECR image URI.
2. Understand how EKS worker nodes authenticate to ECR.
3. Update an existing Kubernetes Deployment image.
4. Deploy a private ECR image without embedding registry passwords.
5. Monitor a Kubernetes rolling update.
6. Verify that Pods use the correct ECR image.
7. Access the application through a ClusterIP Service.
8. Troubleshoot common ECR image pull errors.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧰 Prerequisites

Ensure you have:

- Completed Lab 03004 — Push a Custom Docker Image to Amazon ECR.
- An active EKS cluster named `vyomanant-eks`.
- Managed EC2 node group `workers`.
- Namespace `vyomanant-lab`.
- Deployment `vyomanant-web` from Lab 03003.
- ClusterIP Service `vyomanant-web-service`.
- ECR repository `myapp` containing image tag `web-v1`.
- AWS CLI and kubectl configured.
- EKS worker node IAM role with ECR image pull permissions.

### 🔎 Verify AWS Authentication

```
export AWS_PROFILE=vyomanant
```

```
aws sts get-caller-identity
```

### 🔎 Verify EKS Connectivity

```
kubectl get nodes
```

✅ Expected: Both EKS worker nodes are in the `Ready` state.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🏗️ Lab Architecture

```
                   Amazon ECR
        ┌─────────────────────────────┐
        │ Private Repository: myapp   │
        │ Image Tag: web-v1           │
        └──────────────┬──────────────┘
                       │
                       │ Authenticated Image Pull
                       │ Worker Node IAM Role
                       ▼
┌───────────────────────────────────────────────┐
│ Amazon EKS: vyomanant-eks                     │
│                                               │
│ Namespace: vyomanant-lab                      │
│                                               │
│ Deployment: vyomanant-web                     │
│                                               │
│    ┌────────────────┐ ┌────────────────┐      │
│    │ Web Pod 1      │ │ Web Pod 2      │      │
│    │ ECR: web-v1    │ │ ECR: web-v1    │      │
│    └────────┬───────┘ └───────┬────────┘      │
│             │                 │               │
│             └────────┬────────┘               │
│                      ▼                        │
│       Service: vyomanant-web-service          │
│       Type: ClusterIP                         │
│       Port: 80                                │
└──────────────────────┬────────────────────────┘
                       │
                       │ kubectl port-forward
                       ▼
                localhost:8080
                       │
                       ▼
                 Browser / curl
```

Architecture explanation:

- Amazon ECR stores the private application image.
- EKS worker nodes authenticate using their IAM role.
- The Deployment creates Pods using the ECR image.
- The existing ClusterIP Service selects the application Pods.
- Port forwarding allows temporary local access.

Important: No public AWS load balancer is created in this lab.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🚀 Step-by-Step Instructions

## 🔹 Step 1 — Verify the Private ECR Image

🎯 Objective: Confirm that the image is available in Amazon ECR.

```
aws ecr describe-images --repository-name myapp --image-ids imageTag=web-v1 --region ap-south-1
```

✅ Expected: Image details include the tag `web-v1` and a SHA-256 image digest.

### AWS Console Verification

1. Open AWS Console → Amazon ECR.
2. Select Region Mumbai (`ap-south-1`).
3. Open Private registry → Repositories → myapp.
4. Confirm that image tag `web-v1` exists.

🧠 Why It Matters: Kubernetes cannot deploy an image that is missing from the specified registry.

## 🔹 Step 2 — Identify the ECR Image URI

🎯 Objective: Obtain the full image name used by Kubernetes.

In the ECR Console, open the `web-v1` image and copy its image URI.

### Example

```
123456789012.dkr.ecr.ap-south-1.amazonaws.com/myapp:web-v1
```

| Component      | Description            |
| -------------- | ---------------------- |
| `123456789012` | AWS account ID         |
| `ap-south-1`   | AWS Region             |
| `myapp`        | Private ECR repository |
| `web-v1`       | Application image tag  |

Important: Replace the example AWS account ID with your actual account ID.

## 🔹 Step 3 — Understand EKS Authentication to ECR

🎯 Objective: Understand how EKS pulls private container images.

For our existing EKS cluster, the EC2 worker nodes use their IAM node role to authenticate to Amazon ECR.

The node role needs permissions such as:

| IAM Permission               | Purpose                 |
| ---------------------------- | ----------------------- |
| `ecr:GetAuthorizationToken`  | Authenticate to ECR     |
| `ecr:BatchGetImage`          | Retrieve image metadata |
| `ecr:GetDownloadUrlForLayer` | Download image layers   |

The AWS-managed policy `AmazonEC2ContainerRegistryPullOnly` provides the required read-only ECR permissions.

### Verify in AWS Console

1. Open Amazon EKS → Clusters → vyomanant-eks.
2. Select the Compute tab.
3. Open the managed node group `workers`.
4. Identify its node IAM role.
5. Open the role in IAM.
6. Review the attached policies.

✅ Expected: The node role has permissions to pull images from ECR.

Important: The ECR repository and EKS cluster are in the same AWS account in this lab. Cross-account ECR access requires additional repository permissions.

## 🔹 Step 4 — Verify the Existing Kubernetes Deployment

🎯 Objective: Inspect the application deployed in Lab 03003.

```
kubectl get deployments -n vyomanant-lab
```

Expected (illustrative):

```
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
nginx-deployment   2/2     2            2           2h
vyomanant-web      2/2     2            2           30m
```

Inspect the Deployment:

```
kubectl describe deployment vyomanant-web -n vyomanant-lab
```

🔎 Verify: The current container image references Docker Hub.

🧠 Why It Matters: We will update the existing Deployment rather than creating another application.

## 🔹 Step 5 — Update the Deployment to Use Amazon ECR

🎯 Objective: Replace the Docker Hub image with the private ECR image.

Run the following command after replacing the example AWS account ID:

```
kubectl set image deployment/vyomanant-web vyomanant-web=123456789012.dkr.ecr.ap-south-1.amazonaws.com/myapp:web-v1 -n vyomanant-lab
```

Command explanation:

- `set image` updates the container image.
- `deployment/vyomanant-web` identifies the existing Deployment.
- `vyomanant-web=` identifies the container to update.
- The ECR URI specifies the private image.

✅ Expected:

```
deployment.apps/vyomanant-web image updated
```

Important: This command changes the live Deployment. Update the original `deployment.yaml` with the same ECR image URI before reapplying that file in a later lab; otherwise, the old Docker Hub image could be restored.

## 🔹 Step 6 — Monitor the Rolling Update

🎯 Objective: Verify that Kubernetes successfully replaces the application Pods.

```
kubectl rollout status deployment/vyomanant-web -n vyomanant-lab --timeout=180s
```

✅ Expected:

```
deployment "vyomanant-web" successfully rolled out
```

Check the Pods:

```
kubectl get pods -n vyomanant-lab
```

Expected: Two `vyomanant-web` Pods are running.

🧠 Why It Matters: Kubernetes performs a rolling update when the Deployment's container image changes.

## 🔹 Step 7 — Verify the Image Used by the Pods

🎯 Objective: Confirm that the application is running from Amazon ECR.

```
kubectl describe deployment vyomanant-web -n vyomanant-lab
```

Look for the container image:

```
Image: 123456789012.dkr.ecr.ap-south-1.amazonaws.com/myapp:web-v1
```

✅ Expected: The Deployment references the private ECR image.

### Verify in AWS Console

1. Open Amazon EKS → Clusters → vyomanant-eks.
2. Open Resources → Workloads → Deployments, if available.
3. Select the `vyomanant-lab` namespace.
4. Inspect `vyomanant-web`.

Note: Viewing Kubernetes resources through the EKS Console requires appropriate cluster access permissions.

## 🔹 Step 8 — Access the Application

🎯 Objective: Verify that the ECR-based application works.

First, confirm the existing Service:

```
kubectl get services -n vyomanant-lab
```

Expected: `vyomanant-web-service` exists with type `ClusterIP`.

Start port forwarding:

```
kubectl port-forward service/vyomanant-web-service 8080:80 -n vyomanant-lab
```

Keep this terminal running.

Open another terminal:

```
curl http://localhost:8080
```

✅ Expected: The response includes:

```
Welcome to Vyomanant Academy
Docker + Kubernetes + AWS DevOps
Application Version: v1
Container CI/CD Integration Lab
```

Alternatively, open:

```
http://localhost:8080
```

Stop port forwarding with Ctrl+C.

🔎 Observation: The application behaves the same whether its image is pulled from Docker Hub or Amazon ECR.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🔎 Verification

| Check               | Expected Result         |
| ------------------- | ----------------------- |
| ECR repository      | `myapp`                 |
| ECR image           | `web-v1`                |
| Node IAM role       | ECR pull permissions    |
| Deployment          | `vyomanant-web`         |
| Image source        | Private Amazon ECR      |
| Replicas            | 2/2 Ready               |
| Application Pods    | Running                 |
| Service             | `vyomanant-web-service` |
| Service type        | ClusterIP               |
| Application access  | Successful              |
| Application version | `v1`                    |

Lab completion criteria: The existing Kubernetes Deployment successfully pulls and runs the private ECR image, and the application is accessible through its ClusterIP Service.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🛠️ Troubleshooting

| Issue                              | Possible Cause                                 | Resolution                                 |
| ---------------------------------- | ---------------------------------------------- | ------------------------------------------ |
| `ImagePullBackOff`                 | Incorrect image URI or missing ECR permissions | Verify ECR image and node IAM role         |
| `AccessDenied` when pulling        | Node role lacks ECR permissions                | Review the node IAM policies               |
| Rollout fails                      | New Pods cannot start                          | Inspect Deployment and Pod events          |
| Application not accessible         | Pods not ready or Service selector mismatch    | Check Pods and Service                     |
| Old Docker Hub image still running | Image update did not complete                  | Verify rollout status and Deployment image |

### 🔎 Inspect Pod Details

```
kubectl describe pods -n vyomanant-lab
```

### 🔎 Check Application Logs

```
kubectl logs deployment/vyomanant-web -n vyomanant-lab
```

### 🔎 Check Recent Events

```
kubectl get events -n vyomanant-lab
```

Technical Note: An image pull failure can also occur when EKS nodes lack network connectivity to ECR or S3. In this training cluster, worker nodes use public networking, so their outbound connectivity must be functional.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧹 Cleanup

Keep all application resources for the upcoming GitHub Actions labs.

| Resource                            | Action |
| ----------------------------------- | ------ |
| ECR image `myapp:web-v1`            | Keep   |
| ECR repository `myapp`              | Keep   |
| Docker Hub image `vyomanant-web:v1` | Keep   |
| EKS cluster `vyomanant-eks`         | Keep   |
| Namespace `vyomanant-lab`           | Keep   |
| Deployment `vyomanant-web`          | Keep   |
| Service `vyomanant-web-service`     | Keep   |

No resource deletion is required.

⚠️ Cost Reminder: Amazon EKS, EC2 worker nodes, and ECR storage may continue generating AWS charges.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧠 Key Takeaways

1. Amazon ECR securely stores private container images.
2. EKS managed EC2 worker nodes use IAM permissions to pull private ECR images.
3. Private ECR images in the same AWS account normally do not require Kubernetes image pull Secrets.
4. `kubectl set image` updates an existing Deployment.
5. Kubernetes performs rolling updates when the container image changes.
6. Deployment details help verify the configured image source.
7. ClusterIP Services provide stable internal application access.
8. The same Docker application can run from Docker Hub or Amazon ECR.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 📚 Official Documentation

- [Amazon ECR Private Repositories](https://docs.aws.amazon.com/AmazonECR/latest/userguide/Repositories.html?utm_source=chatgpt.com)
- [Amazon EKS Node IAM Role](https://docs.aws.amazon.com/eks/latest/userguide/create-node-role.html?utm_source=chatgpt.com)
- [Amazon ECR IAM Permissions](https://docs.aws.amazon.com/AmazonECR/latest/userguide/security_iam_id-based-policy-examples.html?utm_source=chatgpt.com)
- [Kubernetes Container Images](https://kubernetes.io/docs/concepts/containers/images/?utm_source=chatgpt.com)
- [Kubernetes Deployments](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/?utm_source=chatgpt.com)
- [kubectl Set Image](https://kubernetes.io/docs/reference/kubectl/generated/kubectl_set/kubectl_set_image/?utm_source=chatgpt.com)
- [Kubernetes Port Forwarding](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/?utm_source=chatgpt.com)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Next Lab: 03006 — Configure GitHub Repository, GitHub Actions & AWS OIDC Authentication

We will create a GitHub repository for the application, configure AWS IAM OIDC authentication for GitHub Actions, and prepare secure access to Amazon ECR and Amazon EKS without storing permanent AWS access keys.
