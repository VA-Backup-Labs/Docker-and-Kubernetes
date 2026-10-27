# 🧪 Lab 01002 — Create and Configure an Amazon ECR Private Repository Using AWS Console

Create your first Amazon ECR private repository, configure essential repository settings, and explore its features using the AWS Management Console.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🌍 Lab Overview

Amazon Elastic Container Registry (ECR) provides private repositories for storing Docker container images.

In this lab, you will create an ECR repository using the AWS Management Console, explore image tagging and scanning settings, and prepare the repository for Docker image uploads in the next lab.

| Property     | Details                |
| ------------ | ---------------------- |
| Lab ID       | 01002                  |
| Module       | 01 — Amazon ECR        |
| Difficulty   | Beginner               |
| Duration     | 20–25 minutes          |
| Tools        | AWS Management Console |
| Environment  | AWS Account            |
| Approach     | Console Only           |
| AWS Resource | ECR Private Repository |

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🎯 Learning Objectives

By the end of this lab, you will be able to:

1. Navigate to Amazon ECR using the AWS Console.
2. Create a private ECR repository.
3. Understand repository naming conventions.
4. Configure image tag mutability.
5. Understand image encryption and scanning.
6. Explore repository details and image storage.
7. Locate Docker push commands in the AWS Console.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧰 Prerequisites

Ensure you have:

- An active AWS account.
- AWS Management Console access.
- IAM permissions to create and view ECR repositories.
- Completed Lab 01001 — AWS ECR Fundamentals, IAM Permissions & Prerequisites.
- Selected the AWS Region you will use throughout the course.

Required IAM permissions include:

- `ecr:CreateRepository`
- `ecr:DescribeRepositories`
- `ecr:ListImages`
- `ecr:DescribeImages`

Note: Some Console operations may require additional read permissions.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🏗️ Lab Architecture

```
┌──────────────────────────────┐
│      AWS Management Console  │
│                              │
│        IAM Identity          │
└──────────────┬───────────────┘
               │
               │ Create Repository
               ▼
┌──────────────────────────────┐
│        Amazon ECR            │
│        Private Registry      │
│                              │
│  ┌────────────────────────┐  │
│  │ Repository: myapp      │  │
│  │                        │  │
│  │ Image Tags: Mutable    │  │
│  │ Encryption: AES-256    │  │
│  │ Images: Empty          │  │
│  └────────────────────────┘  │
└──────────────────────────────┘
```

Architecture explanation: The AWS Console creates a private ECR repository named `myapp`. No Docker images are uploaded during this lab.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🚀 Step-by-Step Instructions

## 🔹 Step 1 — Open Amazon ECR

🎯 Objective: Navigate to the Amazon ECR service.

1. Sign in to the [AWS Management Console](https://console.aws.amazon.com/?utm_source=chatgpt.com).
2. In the AWS search bar, search for ECR.
3. Select Elastic Container Registry.
4. In the navigation menu, select Private registry → Repositories.
5. Confirm that the correct AWS Region is selected.

✅ Expected: The private ECR repositories page is displayed.

🧠 Why It Matters: Private ECR repositories are Region-specific. You must use the same Region when pushing Docker images and deploying applications to EKS.

## 🔹 Step 2 — Create an ECR Private Repository

🎯 Objective: Create a repository for storing application images.

1. Click Create repository.
2. Select Private if a repository visibility option is displayed.
3. Enter the following repository details.

| Setting              | Value                               |
| -------------------- | ----------------------------------- |
| Repository name      | `myapp`                             |
| Image tag mutability | Mutable                             |
| Encryption           | AES-256                             |
| Image scanning       | Use registry scanning configuration |

4. Review the settings.
5. Click Create repository.

✅ Expected: A new private repository named `myapp` appears in the repository list.

🧠 Why It Matters: The repository will store the Docker images built in subsequent labs.

## 🔹 Step 3 — Understand Image Tag Mutability

🎯 Objective: Understand how ECR manages Docker image versions.

Amazon ECR supports different image tag mutability configurations.

| Setting                   | Behavior                                  |
| ------------------------- | ----------------------------------------- |
| Mutable                   | Existing image tags can be overwritten    |
| Immutable                 | Existing image tags cannot be overwritten |
| Immutable with exclusions | Selected tags can be overwritten          |

For this beginner lab, use:

Image tag mutability: Mutable

Example image tags:

```
myapp:v1
myapp:v2
myapp:latest
```

⚠️ Production Best Practice: Immutable tags or image digests are preferable for release traceability and predictable deployments. We will explore image versioning in a later lab.

✅ Expected: You understand why mutable tags are convenient for learning but require care in production.

## 🔹 Step 4 — Explore Repository Details

🎯 Objective: Inspect the newly created ECR repository.

1. Open Amazon ECR → Private registry → Repositories.
2. Select the `myapp` repository.
3. Explore the available repository details.

### Important Repository Properties

| Property             | Description                          |
| -------------------- | ------------------------------------ |
| Repository name      | `myapp`                              |
| Repository URI       | Address used to push and pull images |
| Image tag mutability | Controls tag overwriting             |
| Encryption           | Protects stored image data           |
| Repository policy    | Controls repository-level access     |
| Images               | Displays uploaded container images   |

✅ Expected: The `myapp` repository exists and contains no images.

## 🔹 Step 5 — Identify the ECR Repository URI

🎯 Objective: Understand the repository address used by Docker.

The repository URI follows this format:

```
<account-id>.dkr.ecr.<region>.amazonaws.com/myapp
```

Example (illustrative):

```
123456789012.dkr.ecr.ap-south-1.amazonaws.com/myapp
```

| Component      | Description            |
| -------------- | ---------------------- |
| `123456789012` | Example AWS account ID |
| `ap-south-1`   | Example AWS Region     |
| `myapp`        | ECR repository name    |

Important: Your actual repository URI will contain your own AWS account ID and selected Region.

✅ Expected: You can locate and identify the ECR repository URI in the AWS Console.

## 🔹 Step 6 — Explore Docker Push Commands

🎯 Objective: Locate the instructions AWS provides for uploading Docker images.

1. Open the `myapp` repository.
2. Click View push commands.
3. Review the displayed instructions.

The Console provides commands for:

1. Authenticating Docker to Amazon ECR.
2. Building a Docker image.
3. Tagging the Docker image.
4. Pushing the image to ECR.

⚠️ Do not execute the commands yet.

We will execute the complete Docker image build, tag, push, and pull workflow in Lab 01003.

✅ Expected: You understand where to obtain repository-specific Docker push commands.

## 🔹 Step 7 — Explore Image Scanning and Encryption

🎯 Objective: Understand basic ECR image security features.

### Image Scanning

1. Open Amazon ECR → Private registry.
2. Locate the registry's Scanning settings.
3. Review the available scanning configurations.

| Scanning Type     | Purpose                                                                          |
| ----------------- | -------------------------------------------------------------------------------- |
| Basic scanning    | Detects supported image vulnerabilities                                          |
| Enhanced scanning | Integrates with Amazon Inspector for more comprehensive vulnerability management |

Note: Scanning configuration and billing depend on the selected scanning mode. Do not enable enhanced scanning unless it is required and approved for the lab.

### Encryption

1. Return to the `myapp` repository.
2. Review the repository encryption configuration.

For this lab:

Encryption: AES-256

ECR encrypts repository image data at rest. AWS KMS encryption is also supported, but is not required for this lab.

✅ Expected: You can identify the repository's encryption setting and locate registry scanning options.

## 🔹 Step 8 — Verify the Repository Is Ready

🎯 Objective: Confirm the repository is available for future image uploads.

1. Navigate to Amazon ECR → Private registry → Repositories.
2. Confirm the repository `myapp` exists.
3. Open the repository.
4. Verify that the image list is empty.
5. Confirm the repository URI is displayed.
6. Confirm the configured image tag mutability and encryption settings.

✅ Expected: The repository is successfully created and ready for Docker image uploads.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🔎 Verification

| Verification           | Expected Result |
| ---------------------- | --------------- |
| ECR private repository | Created         |
| Repository name        | `myapp`         |
| Repository visibility  | Private         |
| Image tag mutability   | Mutable         |
| Encryption             | AES-256         |
| Repository URI         | Available       |
| Docker push commands   | Accessible      |
| Uploaded images        | 0               |

Lab completion criteria: The `myapp` private repository exists in the selected AWS Region and is ready to receive Docker images.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🛠️ Troubleshooting

| Issue                                   | Possible Cause                                   | Resolution                                                     |
| --------------------------------------- | ------------------------------------------------ | -------------------------------------------------------------- |
| Access denied while creating repository | Missing IAM permissions                          | Request `ecr:CreateRepository` permission                      |
| Repository already exists               | Duplicate repository name                        | Reuse the existing lab repository after verifying its settings |
| Repository not visible                  | Incorrect AWS Region                             | Switch to the Region used during creation                      |
| Cannot view push commands               | Console access or permissions issue              | Verify ECR access and repository selection                     |
| Scanning settings unavailable           | Missing permissions or unsupported configuration | Verify registry scanning permissions and Region                |

### Common Error — Repository Already Exists

Example error:

```
RepositoryAlreadyExistsException
```

Cause: A repository with the same name already exists in the registry.

Fix: Return to the ECR repository list and inspect the existing `myapp` repository. Do not create unnecessary duplicate repositories.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧹 Cleanup

Do not delete the `myapp` repository yet.

It will be reused in the next labs for:

- Docker image push and pull.
- Image tagging and versioning.
- Image lifecycle management.
- ECR and EKS integration.

If you are ending the training and no longer need the repository, you can delete it using the AWS Console after confirming that no other applications depend on it.

⚠️ Warning: Deleting an ECR repository can permanently remove its stored images. Delete only resources created for this training.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧠 Key Takeaways

1. Amazon ECR provides managed private container image repositories.
2. ECR repositories can be created using the AWS Management Console.
3. Repository names identify where container images are stored.
4. Image tag mutability controls whether tags can be overwritten.
5. Repository URIs are required for Docker image push and pull operations.
6. ECR supports image vulnerability scanning and encryption at rest.
7. Docker push commands are available directly in the ECR Console.
8. The `myapp` repository is now ready for upcoming hands-on labs.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 📚 Official Documentation

- [Amazon ECR Documentation](https://docs.aws.amazon.com/AmazonECR/latest/userguide/what-is-ecr.html?utm_source=chatgpt.com)
- [Creating an ECR Private Repository](https://docs.aws.amazon.com/AmazonECR/latest/userguide/repository-create.html?utm_source=chatgpt.com)
- [ECR Image Tag Mutability](https://docs.aws.amazon.com/AmazonECR/latest/userguide/image-tag-mutability.html?utm_source=chatgpt.com)
- [Amazon ECR Image Scanning](https://docs.aws.amazon.com/AmazonECR/latest/userguide/image-scanning.html?utm_source=chatgpt.com)
- [Amazon ECR Encryption at Rest](https://docs.aws.amazon.com/AmazonECR/latest/userguide/encryption-at-rest.html?utm_source=chatgpt.com)
- [Pushing Docker Images to ECR](https://docs.aws.amazon.com/AmazonECR/latest/userguide/docker-push-ecr-image.html?utm_source=chatgpt.com)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Next Lab: 01003 — Build, Tag, Push & Pull Docker Images Using Amazon ECR
