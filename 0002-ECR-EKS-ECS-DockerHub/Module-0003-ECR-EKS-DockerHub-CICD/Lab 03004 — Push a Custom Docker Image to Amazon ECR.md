# 🧪 Lab 03004 — Push a Custom Docker Image to Amazon ECR

Authenticate Docker with Amazon ECR, tag your existing Vyomanant Academy web application image, push it to a private ECR repository, and verify the image in the AWS Console.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🌍 Lab Overview

In Lab 03002, we built a custom Docker image and pushed it to Docker Hub. In Lab 03003, we deployed that image to Amazon EKS.

Now we will publish the same Docker image to Amazon Elastic Container Registry (ECR).

This demonstrates how one application image can be stored in multiple registries without rebuilding it.

| Property       | Details                                  |
| -------------- | ---------------------------------------- |
| Lab ID         | 03004                                    |
| Module         | 03 — Container CI/CD Integration         |
| Difficulty     | Beginner                                 |
| Duration       | 20–25 minutes                            |
| Tools          | Docker, AWS CLI, Amazon ECR, AWS Console |
| Environment    | Ubuntu Linux + AWS                       |
| AWS Region     | `ap-south-1`                             |
| ECR Repository | `myapp`                                  |
| Source Image   | `vyomanant-web:v1`                       |
| Target Image   | `myapp:v1`                               |
| Registry Type  | Private ECR                              |

🎯 Real-World Scenario: A DevOps team needs to publish a tested application image to its organization's private AWS container registry for deployment on Amazon EKS.

Important: The existing `myapp` repository may already contain the `v1` tag from Module 01. Because that repository was configured with immutable image tags, we will use a new tag, `web-v1`, to avoid overwriting it.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🎯 Learning Objectives

By the end of this lab, you will be able to:

1. Understand private Amazon ECR image repositories.
2. Verify an existing ECR repository.
3. Authenticate Docker with Amazon ECR.
4. Identify the ECR repository URI.
5. Tag an existing Docker image for ECR.
6. Push a Docker image to a private ECR repository.
7. Verify the published image using AWS CLI and Console.
8. Understand ECR image tag immutability.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧰 Prerequisites

Ensure you have:

- Completed Lab 03003 — Deploy a Docker Hub Image to Amazon EKS.
- Docker installed and running.
- AWS CLI configured.
- AWS CLI profile named `vyomanant`.
- An existing private ECR repository named `myapp`.
- The local Docker image `vyomanant-web:v1`.
- IAM permissions to authenticate and push images to ECR.

### 🔎 Verify Docker

```
docker images
```

✅ Expected: The image `vyomanant-web:v1` exists locally.

### 🔎 Verify AWS Authentication

```
export AWS_PROFILE=vyomanant
```

```
aws sts get-caller-identity
```

✅ Expected: Your AWS account and authenticated identity are displayed.

Note: If your AWS IAM Identity Center session has expired, sign in again using the configured profile before continuing.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🏗️ Lab Architecture

```
                   Ubuntu Linux
┌─────────────────────────────────────────────┐
│                                             │
│  Local Docker Image                         │
│  vyomanant-web:v1                           │
│          │                                  │
│          │ docker tag                       │
│          ▼                                  │
│  ECR-Tagged Docker Image                    │
│  <account-id>.dkr.ecr.ap-south-1            │
│  .amazonaws.com/myapp:web-v1                │
│          │                                  │
│          │ docker push                      │
│          ▼                                  │
└──────────┼──────────────────────────────────┘
           │
           ▼
┌─────────────────────────────────────────────┐
│              Amazon ECR                     │
│                                             │
│  Private Repository: myapp                  │
│                                             │
│  Existing Image: v1                         │
│  New Image:      web-v1                     │
│                                             │
└─────────────────────────────────────────────┘

            Next Lab: 03005
                     │
                     ▼
                Amazon EKS
```

Architecture explanation:

- Docker uses the existing application image.
- The image receives a new tag containing the ECR repository URI.
- Docker authenticates to ECR using AWS credentials.
- The image is pushed to the private repository `myapp`.
- Amazon EKS will pull this image in Lab 03005.

Important: No new ECR repository or EKS cluster is created.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🚀 Step-by-Step Instructions

## 🔹 Step 1 — Verify the Existing ECR Repository

🎯 Objective: Confirm that the private repository `myapp` exists.

```
aws ecr describe-repositories --repository-names myapp --region ap-south-1
```

✅ Expected: AWS returns repository information, including its repository URI.

### Verify in AWS Console

1. Open the [AWS Management Console](https://console.aws.amazon.com/?utm_source=chatgpt.com).
2. Select Region Mumbai (`ap-south-1`).
3. Navigate to Amazon ECR → Private registry → Repositories.
4. Open `myapp`.

Confirm the repository exists.

🧠 Why It Matters: The destination repository must exist before Docker can push the image.

## 🔹 Step 2 — Identify Your AWS Account ID

🎯 Objective: Obtain the account ID required for the ECR registry address.

```
aws sts get-caller-identity
```

Expected (illustrative):

```
{
    "UserId": "...",
    "Account": "123456789012",
    "Arn": "..."
}
```

Your ECR repository URI follows this format:

```
123456789012.dkr.ecr.ap-south-1.amazonaws.com/myapp
```

Important: Replace `123456789012` in subsequent commands with your actual 12-digit AWS account ID.

## 🔹 Step 3 — Authenticate Docker to Amazon ECR

🎯 Objective: Allow Docker to push images to your private ECR repository.

```
aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin 123456789012.dkr.ecr.ap-south-1.amazonaws.com
```

Command explanation:

- `get-login-password` retrieves an ECR authentication password.
- `docker login` authenticates Docker to the ECR registry.
- `--password-stdin` reads the password securely from standard input.

✅ Expected:

```
Login Succeeded
```

🔐 Security Note: ECR authorization tokens are temporary. Do not save the password in scripts or GitHub repositories.

## 🔹 Step 4 — Verify the Existing Application Image

🎯 Objective: Confirm the image built in Lab 03002 is available.

```
docker images
```

Expected (illustrative):

```
REPOSITORY       TAG   IMAGE ID       CREATED        SIZE
vyomanant-web    v1    a1b2c3d4e5f6   20 minutes ago ...
```

Important: If the image is missing, rebuild it from the `vyomanant-web` application directory created in Lab 03002 before continuing.

## 🔹 Step 5 — Tag the Docker Image for ECR

🎯 Objective: Assign the image its private ECR destination.

```
docker tag vyomanant-web:v1 123456789012.dkr.ecr.ap-south-1.amazonaws.com/myapp:web-v1
```

Verify:

```
docker images
```

Expected (illustrative):

```
REPOSITORY                                                TAG      IMAGE ID
vyomanant-web                                             v1       a1b2c3d4e5f6
123456789012.dkr.ecr.ap-south-1.amazonaws.com/myapp        web-v1   a1b2c3d4e5f6
```

🔎 Observation: Both image tags reference the same local image ID.

🧠 Why It Matters: `docker tag` creates another reference to the existing image. It does not rebuild the application.

## 🔹 Step 6 — Push the Image to Amazon ECR

🎯 Objective: Upload the custom application image to the private registry.

```
docker push 123456789012.dkr.ecr.ap-south-1.amazonaws.com/myapp:web-v1
```

Expected (illustrative):

```
The push refers to repository
[123456789012.dkr.ecr.ap-south-1.amazonaws.com/myapp]

web-v1: digest: sha256:... size: ...
```

✅ Expected: Docker successfully pushes the image layers and manifest to Amazon ECR.

Technical Note: If the tag `web-v1` already exists in an immutable repository, ECR may reject the push. Use a new unused tag and carry that tag consistently into Lab 03005.

## 🔹 Step 7 — Verify the Image in AWS Console

🎯 Objective: Confirm the image is stored in Amazon ECR.

1. Open AWS Console → Amazon ECR.
2. Select Private registry → Repositories.
3. Open `myapp`.
4. Select the Images tab.
5. Locate the image tagged `web-v1`.

### Expected Image Details

| Property     | Expected                         |
| ------------ | -------------------------------- |
| Repository   | `myapp`                          |
| Image tag    | `web-v1`                         |
| Registry     | Private                          |
| Image digest | SHA-256 digest                   |
| Image URI    | ECR repository URI with `web-v1` |

✅ Expected: The custom Vyomanant Academy application image appears in the repository.

## 🔹 Step 8 — Verify the Image Using AWS CLI

🎯 Objective: Confirm that AWS recognizes the published image.

```
aws ecr describe-images --repository-name myapp --image-ids imageTag=web-v1 --region ap-south-1
```

Expected (illustrative):

```
{
    "imageDetails": [
        {
            "registryId": "123456789012",
            "repositoryName": "myapp",
            "imageTags": [
                "web-v1"
            ],
            "imageDigest": "sha256:..."
        }
    ]
}
```

🔎 Verify: The image tag `web-v1` and an image digest are present.

🧠 Why It Matters: The image digest identifies the stored image content, while the tag provides a readable version reference.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🔎 Verification

| Check                    | Expected Result        |
| ------------------------ | ---------------------- |
| AWS authentication       | Successful             |
| ECR repository           | `myapp`                |
| Docker authentication    | Successful             |
| Local source image       | `vyomanant-web:v1`     |
| ECR image tag            | `web-v1`               |
| Docker image push        | Successful             |
| ECR Console verification | Image visible          |
| AWS CLI verification     | Image details returned |
| Existing EKS cluster     | Unchanged              |

Lab completion criteria: The custom Docker image is successfully stored in Amazon ECR under the tag `web-v1`.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🛠️ Troubleshooting

| Issue                            | Possible Cause                                | Resolution                   |
| -------------------------------- | --------------------------------------------- | ---------------------------- |
| `no basic auth credentials`      | Docker is not authenticated to ECR            | Repeat the ECR login command |
| `AccessDeniedException`          | Missing IAM permissions                       | Verify ECR push permissions  |
| `repository does not exist`      | Incorrect account, Region, or repository name | Verify the repository URI    |
| `tag invalid` or image not found | Incorrect local image reference               | Check `docker images`        |
| Image push rejected              | Immutable tag already exists                  | Use a new unused image tag   |

### 🔎 Verify Local Images

```
docker images
```

### 🔎 Verify ECR Repository

```
aws ecr describe-repositories --repository-names myapp --region ap-south-1
```

Important: Do not disable repository tag immutability simply to overwrite an existing training image.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧹 Cleanup

Keep the ECR image for Lab 03005.

| Resource                            | Action |
| ----------------------------------- | ------ |
| Local image `vyomanant-web:v1`      | Keep   |
| ECR image `myapp:web-v1`            | Keep   |
| ECR repository `myapp`              | Keep   |
| Docker Hub image `vyomanant-web:v1` | Keep   |
| EKS cluster `vyomanant-eks`         | Keep   |
| Namespace `vyomanant-lab`           | Keep   |
| Deployment `vyomanant-web`          | Keep   |

⚠️ Cost Reminder: Amazon ECR charges for stored image data, while your EKS cluster and EC2 worker nodes continue generating AWS charges.

Do not delete the ECR image yet. It will be deployed to Amazon EKS in the next lab.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧠 Key Takeaways

1. Amazon ECR provides private container image repositories.
2. Docker authenticates to ECR using AWS-issued temporary credentials.
3. An existing Docker image can be published to multiple registries.
4. `docker tag` creates a new image reference without rebuilding.
5. `docker push` uploads the image to the selected registry.
6. ECR repository URIs contain the AWS account ID, Region, and repository name.
7. Immutable image tags prevent accidental replacement of existing versions.
8. ECR image digests identify stored image content.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 📚 Official Documentation

- [Amazon ECR User Guide](https://docs.aws.amazon.com/AmazonECR/latest/userguide/what-is-ecr.html?utm_source=chatgpt.com)
- [Private Registry Authentication](https://docs.aws.amazon.com/AmazonECR/latest/userguide/registry_auth.html?utm_source=chatgpt.com)
- [Pushing Docker Images to ECR](https://docs.aws.amazon.com/AmazonECR/latest/userguide/docker-push-ecr-image.html?utm_source=chatgpt.com)
- [ECR Image Tag Mutability](https://docs.aws.amazon.com/AmazonECR/latest/userguide/image-tag-mutability.html?utm_source=chatgpt.com)
- [Docker Image Tag](https://docs.docker.com/reference/cli/docker/image/tag/?utm_source=chatgpt.com)
- [Docker Image Push](https://docs.docker.com/reference/cli/docker/image/push/?utm_source=chatgpt.com)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Next Lab: 03005 — Deploy a Private Amazon ECR Image to EKS & Expose It

We will update the Kubernetes Deployment to use the private ECR image, verify that EKS pulls it successfully, and access the application using its existing ClusterIP Service.
