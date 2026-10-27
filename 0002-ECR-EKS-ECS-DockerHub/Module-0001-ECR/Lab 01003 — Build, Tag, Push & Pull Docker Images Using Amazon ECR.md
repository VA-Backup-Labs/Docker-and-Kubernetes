# 🧪 Lab 01003 — Build, Tag, Push & Pull Docker Images Using Amazon ECR

Build a Docker image, authenticate Docker with AWS ECR, push the image to a private repository, and pull it back to your local machine.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🌍 Lab Overview

In this lab, you will use Docker CLI and Amazon ECR to complete a container image publishing workflow.

You will reuse the `myapp` repository created in Lab 01002. AWS Console will be used to inspect the repository, while Docker CLI and AWS CLI will be used only for essential image operations.

| Property    | Details                          |
| ----------- | -------------------------------- |
| Lab ID      | 01003                            |
| Module      | 01 — Amazon ECR                  |
| Difficulty  | Beginner                         |
| Duration    | 25–30 minutes                    |
| Tools       | AWS Console, Docker CLI, AWS CLI |
| Environment | Local Machine + AWS              |
| Repository  | `myapp`                          |
| Image       | `nginx:latest`                   |

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🎯 Learning Objectives

By the end of this lab, you will be able to:

1. Verify Docker and AWS CLI connectivity.
2. Pull a Docker image from Docker Hub.
3. Understand Docker image tags.
4. Authenticate Docker with Amazon ECR.
5. Tag a Docker image for an ECR repository.
6. Push a Docker image to private ECR.
7. Verify the image using the AWS Console.
8. Pull an image from ECR and run a container.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧰 Prerequisites

Ensure you have:

- Completed Lab 01002 — Create an ECR Private Repository.
- An existing ECR repository named `myapp`.
- Docker installed and running.
- AWS CLI v2 installed and authenticated.
- IAM permissions to push and pull images from ECR.
- A Bash-compatible terminal.

### Required IAM Permissions

Your AWS identity must have the necessary ECR permissions, including:

- `ecr:GetAuthorizationToken`
- `ecr:BatchCheckLayerAvailability`
- `ecr:InitiateLayerUpload`
- `ecr:UploadLayerPart`
- `ecr:CompleteLayerUpload`
- `ecr:PutImage`
- `ecr:BatchGetImage`
- `ecr:GetDownloadUrlForLayer`

Note: The commands below use the example Region `ap-south-1`. Replace it with your selected AWS Region if different. Replace the example account ID `123456789012` with your actual AWS account ID.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🏗️ Lab Architecture

```
┌──────────────────────────────┐
│       Docker Hub             │
│       nginx:latest           │
└──────────────┬───────────────┘
               │ Docker Pull
               ▼
┌──────────────────────────────┐
│       Local Machine          │
│                              │
│  Docker Image: nginx:latest  │
│              │               │
│              ▼               │
│  Tag Image for AWS ECR       │
└──────────────┬───────────────┘
               │ Docker Push
               ▼
┌──────────────────────────────┐
│       Amazon ECR             │
│       Private Registry       │
│                              │
│  Repository: myapp           │
│  Image Tag: v1               │
└──────────────┬───────────────┘
               │ Docker Pull
               ▼
┌──────────────────────────────┐
│       Local Machine          │
│                              │
│  Container: ecr-web          │
│  Port: 8080 → 80             │
└──────────────────────────────┘
```

Architecture explanation: The NGINX image is downloaded from Docker Hub, tagged and uploaded to Amazon ECR, then pulled from ECR and used to run a local container.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🚀 Step-by-Step Instructions

## 🔹 Step 1 — Verify Docker and AWS Access

🎯 Objective: Confirm that Docker and AWS CLI are ready.

💻 Commands:

```
docker --version
```

```
aws sts get-caller-identity
```

✅ Expected:

- Docker version information is displayed.
- AWS account and identity information is returned.

🧠 Why It Matters: Docker performs image operations, while AWS authentication provides access to your private ECR registry.

## 🔹 Step 2 — Pull an NGINX Image from Docker Hub

🎯 Objective: Download a container image to your local machine.

💻 Command:

```
docker pull nginx:latest
```

Verify the image:

```
docker images
```

✅ Expected (illustrative):

```
REPOSITORY   TAG       IMAGE ID       SIZE
nginx        latest    abc123def456   190MB
```

Image IDs and sizes may differ.

🧠 Why It Matters: Docker images can be downloaded from public registries and uploaded to private registries for controlled distribution.

## 🔹 Step 3 — Identify Your ECR Repository URI

🎯 Objective: Find the destination for your Docker image.

1. Open AWS Console → Amazon ECR.
2. Select Private registry → Repositories.
3. Open the `myapp` repository.
4. Copy the repository URI.

Example repository URI:

```
123456789012.dkr.ecr.ap-south-1.amazonaws.com/myapp
```

The actual URI will use your AWS account ID and Region.

✅ Expected: You have identified the correct repository URI.

## 🔹 Step 4 — Authenticate Docker with Amazon ECR

🎯 Objective: Allow Docker to push and pull images from your private registry.

First, configure the lab-specific values in your Bash terminal.

```
AWS_REGION=ap-south-1
```

```
AWS_ACCOUNT_ID=$(aws sts get-caller-identity --query Account --output text)
```

```
ECR_URI=$AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/myapp
```

Note: Replace `ap-south-1` if your ECR repository is in another Region. These variables are used in the remaining commands.

💻 Command:

```
aws ecr get-login-password --region $AWS_REGION | docker login --username AWS --password-stdin $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com
```

✅ Expected:

```
Login Succeeded
```

🧠 Why It Matters: Amazon ECR uses temporary authentication tokens to authorize Docker registry operations.

🔐 Security: Do not paste ECR authentication tokens into scripts or commit them to GitHub.

## 🔹 Step 5 — Tag the Docker Image for ECR

🎯 Objective: Assign the image a destination repository and version.

💻 Command:

```
docker tag nginx:latest $ECR_URI:v1
```

Verify:

```
docker images
```

✅ Expected: Two image references appear:

```
nginx:latest
123456789012.dkr.ecr.ap-south-1.amazonaws.com/myapp:v1
```

Both references point to the same underlying image.

🧠 Why It Matters: Docker image tagging identifies the destination registry, repository, and version.

## 🔹 Step 6 — Push the Docker Image to Amazon ECR

🎯 Objective: Upload the tagged image to your private ECR repository.

💻 Command:

```
docker push $ECR_URI:v1
```

✅ Expected (illustrative):

```
The push refers to repository [...]
...
v1: digest: sha256:...
```

🧠 Why It Matters: The image is now stored in Amazon ECR and can be pulled by authorized clients, including EKS worker nodes.

## 🔹 Step 7 — Verify the Image Using AWS Console

🎯 Objective: Confirm that the image was successfully uploaded.

1. Open AWS Console → Amazon ECR.
2. Navigate to Private registry → Repositories.
3. Select `myapp`.
4. Open the Images section.
5. Locate the image with tag `v1`.

Review:

| Property     | Expected         |
| ------------ | ---------------- |
| Repository   | `myapp`          |
| Image tag    | `v1`             |
| Image digest | SHA-256 digest   |
| Image size   | Displayed        |
| Pushed at    | Upload timestamp |

✅ Expected: The `v1` image is visible in Amazon ECR.

## 🔹 Step 8 — Pull the Image from Amazon ECR

🎯 Objective: Verify that the image can be retrieved from the private registry.

Remove the local ECR-tagged image reference:

```
docker rmi $ECR_URI:v1
```

Note: This removes only the local image reference, not the image stored in ECR. The original `nginx:latest` image remains available locally.

Pull the image from ECR:

```
docker pull $ECR_URI:v1
```

✅ Expected: Docker successfully downloads or reuses the image layers and restores the ECR image reference.

🧠 Why It Matters: This confirms that your ECR image can be retrieved using the authenticated Docker client.

## 🔹 Step 9 — Run the ECR Image as a Docker Container

🎯 Objective: Start a container using the image retrieved from Amazon ECR.

💻 Command:

```
docker run -d --name ecr-web -p 8080:80 $ECR_URI:v1
```

Command explanation:

- `-d` runs the container in the background.
- `--name ecr-web` assigns a container name.
- `-p 8080:80` maps local port 8080 to container port 80.

Verify:

```
docker ps
```

✅ Expected: The `ecr-web` container is running.

Open your browser:

```
http://localhost:8080
```

✅ Expected: The default NGINX welcome page appears.

Note: Ensure local port 8080 is available before running the container.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🔎 Verification

| Verification                        | Expected Result    |
| ----------------------------------- | ------------------ |
| Docker image pulled from Docker Hub | Successful         |
| AWS ECR authentication              | Login Succeeded    |
| Docker image tagged                 | `myapp:v1`         |
| Docker image pushed                 | Successful         |
| Image visible in ECR Console        | Yes                |
| Image pulled from ECR               | Successful         |
| Container running                   | `ecr-web`          |
| Browser access                      | NGINX welcome page |

Lab completion criteria: You have successfully pushed a Docker image to ECR, pulled it back, and started a working container.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🛠️ Troubleshooting

| Issue                       | Possible Cause                     | Resolution                                                     |
| --------------------------- | ---------------------------------- | -------------------------------------------------------------- |
| `no basic auth credentials` | Docker not authenticated to ECR    | Repeat the ECR login command                                   |
| `AccessDeniedException`     | Missing IAM permissions            | Verify the AWS identity and ECR permissions                    |
| `repository does not exist` | Incorrect repository URI or Region | Verify the repository in AWS Console                           |
| `image not found`           | Incorrect image tag                | Check the image tag in ECR                                     |
| Port 8080 already allocated | Another application uses the port  | Stop the conflicting application or use another available port |

### 🔎 Check Container Logs

```
docker logs ecr-web
```

### 🔎 Inspect Container Status

```
docker ps -a
```

These commands help identify container startup errors.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧹 Cleanup

🎯 Objective: Remove only the local container created in this lab.

Stop the container:

```
docker stop ecr-web
```

Remove the container:

```
docker rm ecr-web
```

Verify:

```
docker ps -a
```

✅ Expected: The `ecr-web` container no longer appears.

Important: Keep the `myapp` ECR repository and the `v1` image for the next lab on image versioning and lifecycle policies.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧠 Key Takeaways

1. Docker Hub provides publicly available container images.
2. Amazon ECR stores container images in private AWS repositories.
3. Docker CLI is used to pull, tag, push, and run images.
4. AWS CLI provides temporary Docker authentication for ECR.
5. ECR image URIs contain the AWS account ID, Region, repository, and tag.
6. Image tags such as `v1` help identify application versions.
7. ECR images can be pulled and run like other Docker images.
8. The same ECR image can later be deployed to Amazon EKS.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 📚 Official Documentation

- [Amazon ECR User Guide](https://docs.aws.amazon.com/AmazonECR/latest/userguide/what-is-ecr.html?utm_source=chatgpt.com)
- [Authenticating Docker to Amazon ECR](https://docs.aws.amazon.com/AmazonECR/latest/userguide/registry_auth.html?utm_source=chatgpt.com)
- [Pushing Docker Images to ECR](https://docs.aws.amazon.com/AmazonECR/latest/userguide/docker-push-ecr-image.html?utm_source=chatgpt.com)
- [Pulling Docker Images from ECR](https://docs.aws.amazon.com/AmazonECR/latest/userguide/docker-pull-ecr-image.html?utm_source=chatgpt.com)
- [Docker Image Tagging](https://docs.docker.com/reference/cli/docker/image/tag/?utm_source=chatgpt.com)
- [Docker Container Run](https://docs.docker.com/reference/cli/docker/container/run/?utm_source=chatgpt.com)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Next Lab: 01004 — Manage ECR Image Tags, Versions & Lifecycle Policies Using AWS Console
