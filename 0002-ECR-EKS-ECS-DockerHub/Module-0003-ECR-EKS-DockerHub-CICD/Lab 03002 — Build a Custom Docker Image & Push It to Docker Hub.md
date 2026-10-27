# 🧪 Lab 03002 — Build a Custom Docker Image & Push It to Docker Hub

Create a simple HTML web application, package it using Docker, test it locally, and publish the image to Docker Hub for deployment on Amazon EKS.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🌍 Lab Overview

In this lab, you will build your first custom application image for the Module 03 CI/CD project.

You will create an HTML page, write a Dockerfile using NGINX, build the image, test it locally, and push it to your Docker Hub repository.

The same application will be reused in upcoming Docker Hub, Amazon ECR, EKS, and GitHub Actions labs.

| Property       | Details                                    |
| -------------- | ------------------------------------------ |
| Lab ID         | 03002                                      |
| Module         | 03 — Container CI/CD Integration           |
| Difficulty     | Beginner                                   |
| Duration       | 20–25 minutes                              |
| Tools          | Docker, Docker Hub, Ubuntu Linux           |
| Application    | Static HTML + NGINX                        |
| Docker Image   | `YOUR_DOCKERHUB_USERNAME/vyomanant-web:v1` |
| Container Name | `vyomanant-web`                            |
| Local Port     | `8080`                                     |
| Registry       | Docker Hub                                 |

🎯 Real-World Scenario: A development team needs to package a web application into a Docker image and publish it to a container registry so that Kubernetes can deploy the same application consistently across environments.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🎯 Learning Objectives

By the end of this lab, you will be able to:

1. Create a simple HTML web application.
2. Write a Dockerfile using an NGINX base image.
3. Build a custom Docker image.
4. Understand Docker image names and tags.
5. Run and test a container locally.
6. Authenticate to Docker Hub.
7. Push a Docker image to Docker Hub.
8. Verify the published image.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧰 Prerequisites

Ensure you have:

- Completed Lab 03001 — Container CI/CD Integration Architecture.
- Ubuntu Linux with Docker installed and running.
- A Docker Hub account.
- Permission to create a Docker Hub repository.
- Internet connectivity.
- A terminal with permission to run Docker commands.

### 🔎 Verify Docker

```
docker --version
```

```
docker info
```

✅ Expected: Docker is installed, and the Docker daemon is accessible.

Important: Replace `YOUR_DOCKERHUB_USERNAME` in this lab with your actual Docker Hub username. Use lowercase characters in the repository name.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🏗️ Lab Architecture

```
                  Ubuntu Linux
┌───────────────────────────────────────────┐
│                                           │
│  Application Directory                    │
│  vyomanant-web/                            │
│  ├── index.html                           │
│  └── Dockerfile                           │
│          │                                │
│          ▼                                │
│     docker build                          │
│          │                                │
│          ▼                                │
│  Docker Image                             │
│  vyomanant-web:v1                         │
│          │                                │
│          ├──────────────┐                 │
│          │              │                 │
│          ▼              ▼                 │
│     docker run      docker tag            │
│          │              │                 │
│          ▼              │                 │
│  Local Container        │                 │
│  localhost:8080         │                 │
│                         │                 │
└─────────────────────────┼─────────────────┘
                          │
                          ▼
                     docker push
                          │
                          ▼
              ┌─────────────────────────┐
              │ Docker Hub              │
              │                         │
              │ YOUR_DOCKERHUB_USERNAME │
              │ /vyomanant-web:v1       │
              └─────────────────────────┘
```

Architecture explanation:

- `index.html` contains the application.
- `Dockerfile` defines how to package it using NGINX.
- Docker builds the application image.
- A local container runs the application on port `8080`.
- The image is tagged and pushed to Docker Hub.

Note: Amazon EKS is not used in this lab. We will deploy this image to EKS in Lab 03003.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🚀 Step-by-Step Instructions

## 🔹 Step 1 — Create the Application Directory

🎯 Objective: Prepare a working directory for the application.

```
mkdir vyomanant-web
```

```
cd vyomanant-web
```

✅ Expected: You are inside the `vyomanant-web` directory.

## 🔹 Step 2 — Create the HTML Application

🎯 Objective: Build a simple web page that will run inside an NGINX container.

Create the file:

```
nano index.html
```

Add the following content:

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Vyomanant Academy</title>
</head>
<body>
    <h1>Welcome to Vyomanant Academy</h1>
    <h2>Docker + Kubernetes + AWS DevOps</h2>
    <p>Application Version: v1</p>
    <p>Container CI/CD Integration Lab</p>
</body>
</html>
```

Save the file.

🧠 Why It Matters: This page will help us visually verify which application version is running during later CI/CD deployments.

## 🔹 Step 3 — Create the Dockerfile

🎯 Objective: Package the HTML application using NGINX.

Create the file:

```
nano Dockerfile
```

Add:

```
FROM nginx:1.28-alpine

COPY index.html /usr/share/nginx/html/index.html

EXPOSE 80
```

### Dockerfile Explanation

| Instruction | Purpose                                           |
| ----------- | ------------------------------------------------- |
| `FROM`      | Uses the NGINX base image                         |
| `COPY`      | Copies the HTML page into the NGINX web directory |
| `EXPOSE 80` | Documents the container's HTTP port               |

🧠 Why It Matters: The Dockerfile provides a repeatable way to package the application and its runtime.

## 🔹 Step 4 — Build the Docker Image

🎯 Objective: Create a Docker image containing the application.

```
docker build -t vyomanant-web:v1 .
```

Command explanation:

- `-t` assigns an image name and tag.
- `vyomanant-web:v1` is the local image name.
- `.` uses the current directory as the build context.

✅ Expected: Docker completes the build successfully.

Verify:

```
docker images
```

Expected (illustrative):

```
REPOSITORY      TAG   IMAGE ID       CREATED          SIZE
vyomanant-web   v1    a1b2c3d4e5f6   10 seconds ago   ...
```

🔎 Observation: The custom image is available locally.

## 🔹 Step 5 — Run and Test the Application Locally

🎯 Objective: Verify that the Docker image works before publishing it.

Run the container:

```
docker run -d --name vyomanant-web -p 8080:80 vyomanant-web:v1
```

Command explanation:

- `-d` runs the container in the background.
- `--name` assigns a container name.
- `-p 8080:80` maps host port `8080` to container port `80`.

Verify:

```
docker ps
```

Expected: The `vyomanant-web` container is running.

Test the application:

```
curl http://localhost:8080
```

✅ Expected: The response contains:

```
Welcome to Vyomanant Academy
Docker + Kubernetes + AWS DevOps
Application Version: v1
```

Open the application in a browser on the Docker host:

```
http://localhost:8080
```

Expected: The Vyomanant Academy HTML page appears.

Note: If Docker runs on a remote Ubuntu server, `localhost` refers to that server, not your laptop.

## 🔹 Step 6 — Create a Docker Hub Repository

🎯 Objective: Prepare a repository to store the application image.

### Docker Hub Steps

1. Open [Docker Hub](https://hub.docker.com/?utm_source=chatgpt.com).
2. Sign in.
3. Navigate to My Hub → Repositories.
4. Select Create repository.
5. Enter the repository name:

```
vyomanant-web
```

6. Select Public visibility for this training application.
7. Create the repository.

### Repository Details

| Property    | Value                  |
| ----------- | ---------------------- |
| Repository  | `vyomanant-web`        |
| Visibility  | Public                 |
| Image Tag   | `v1`                   |
| Application | Vyomanant Academy HTML |

⚠️ Security Note: Public Docker Hub images can be downloaded by anyone. Do not include passwords, API keys, or sensitive information in the image.

## 🔹 Step 7 — Log In to Docker Hub

🎯 Objective: Authenticate Docker before pushing the image.

```
docker login
```

Follow the authentication instructions shown in your terminal.

Depending on your Docker installation, Docker may use browser-based device authentication or request a username and credentials.

Recommended: Use a Docker Hub personal access token when password-based CLI authentication is required.

✅ Expected:

```
Login Succeeded
```

Important: Never place Docker Hub passwords or access tokens directly in your Dockerfile or GitHub repository.

## 🔹 Step 8 — Tag the Image for Docker Hub

🎯 Objective: Assign the image its Docker Hub repository name.

Replace `YOUR_DOCKERHUB_USERNAME` with your actual username:

```
docker tag vyomanant-web:v1 YOUR_DOCKERHUB_USERNAME/vyomanant-web:v1
```

Example image format:

```
yourusername/vyomanant-web:v1
```

Verify:

```
docker images
```

✅ Expected: The image appears with both its local name and Docker Hub repository name.

🧠 Why It Matters: Docker uses the repository name to determine where the image should be pushed.

## 🔹 Step 9 — Push the Image to Docker Hub

🎯 Objective: Publish the application image.

```
docker push YOUR_DOCKERHUB_USERNAME/vyomanant-web:v1
```

✅ Expected (illustrative):

```
The push refers to repository [docker.io/yourusername/vyomanant-web]
...
v1: digest: sha256:... size: ...
```

### Verify in Docker Hub

1. Open Docker Hub.
2. Select the `vyomanant-web` repository.
3. Open the Tags section.
4. Verify that tag `v1` exists.

✅ Expected: The custom application image is available in Docker Hub.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🔎 Verification

| Check                 | Expected Result         |
| --------------------- | ----------------------- |
| Application directory | `vyomanant-web`         |
| HTML application      | Created                 |
| Dockerfile            | Created                 |
| Local image           | `vyomanant-web:v1`      |
| Local container       | Running                 |
| Application URL       | `http://localhost:8080` |
| Application version   | `v1`                    |
| Docker Hub repository | `vyomanant-web`         |
| Docker Hub image tag  | `v1`                    |
| Image push            | Successful              |

Lab completion criteria: Your custom application runs successfully in Docker and its image is available in your Docker Hub repository.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🛠️ Troubleshooting

| Issue                                 | Possible Cause                                        | Resolution                                               |
| ------------------------------------- | ----------------------------------------------------- | -------------------------------------------------------- |
| `Cannot connect to the Docker daemon` | Docker service stopped or insufficient permissions    | Check Docker service and user access                     |
| Docker build fails                    | Incorrect Dockerfile or missing HTML file             | Verify both files are in the current directory           |
| Container fails to start              | Port `8080` occupied or container name already exists | Inspect existing containers and port usage               |
| `docker login` fails                  | Authentication problem                                | Retry using Docker Hub authentication or an access token |
| `docker push` returns access denied   | Incorrect username or repository permissions          | Verify the image tag and Docker Hub repository ownership |

### 🔎 Check All Containers

```
docker ps -a
```

### 🔎 Inspect Container Logs

```
docker logs vyomanant-web
```

Expected: NGINX startup messages or application access logs.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧹 Cleanup

🎯 Objective: Stop the local test container while preserving the Docker image for upcoming labs.

Stop the container:

```
docker stop vyomanant-web
```

Remove the container:

```
docker rm vyomanant-web
```

Verify:

```
docker ps -a
```

✅ Expected: The `vyomanant-web` container no longer appears.

### Resources to Keep

| Resource                            | Action |
| ----------------------------------- | ------ |
| `index.html`                        | Keep   |
| `Dockerfile`                        | Keep   |
| Local image `vyomanant-web:v1`      | Keep   |
| Docker Hub image `vyomanant-web:v1` | Keep   |
| ECR repository `myapp`              | Keep   |
| EKS cluster `vyomanant-eks`         | Keep   |

Important: Do not delete the Docker Hub image. It will be used in Lab 03003.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧠 Key Takeaways

1. A Dockerfile defines how to package an application.
2. NGINX can serve static HTML content from a Docker container.
3. `docker build` creates a reusable container image.
4. Docker image tags identify application versions.
5. `docker run` allows local application testing.
6. Docker Hub provides a registry for publishing images.
7. `docker tag` assigns an image its destination repository name.
8. `docker push` publishes the image for deployment on other systems.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 📚 Official Documentation

- [Dockerfile Overview](https://docs.docker.com/build/concepts/dockerfile/?utm_source=chatgpt.com)
- [Docker Build](https://docs.docker.com/reference/cli/docker/buildx/build/?utm_source=chatgpt.com)
- [Docker Run](https://docs.docker.com/reference/cli/docker/container/run/?utm_source=chatgpt.com)
- [Docker Image Tag](https://docs.docker.com/reference/cli/docker/image/tag/?utm_source=chatgpt.com)
- [Docker Image Push](https://docs.docker.com/reference/cli/docker/image/push/?utm_source=chatgpt.com)
- [Docker Hub Repositories](https://docs.docker.com/docker-hub/repos/?utm_source=chatgpt.com)
- [Docker Hub Authentication](https://docs.docker.com/security/for-developers/access-tokens/?utm_source=chatgpt.com)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Next Lab: 03003 — Deploy a Docker Hub Image to Amazon EKS

We will create a Kubernetes Deployment using the custom Docker Hub image, expose it through a ClusterIP Service, and verify that the Vyomanant Academy application runs successfully on Amazon EKS.
