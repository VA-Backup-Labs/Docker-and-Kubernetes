# 🧪 Lab 03003 — Deploy a Docker Hub Image to Amazon EKS

Deploy the custom Vyomanant Academy web application from Docker Hub to Amazon EKS using Kubernetes Deployment and Service manifests.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🌍 Lab Overview

In Lab 03002, we created a custom NGINX-based web application, built its Docker image, and pushed it to Docker Hub.

In this lab, we will deploy that image to our existing Amazon EKS cluster.

We will create a Kubernetes Deployment with two replicas, expose it internally using a ClusterIP Service, and access the application through port forwarding.

| Property     | Details                          |
| ------------ | -------------------------------- |
| Lab ID       | 03003                            |
| Module       | 03 — Container CI/CD Integration |
| Difficulty   | Beginner                         |
| Duration     | 20–25 minutes                    |
| Tools        | Docker Hub, Amazon EKS, kubectl  |
| Cluster      | `vyomanant-eks`                  |
| Namespace    | `vyomanant-lab`                  |
| Application  | Vyomanant Academy Web            |
| Image Source | Docker Hub                       |
| Image Tag    | `v1`                             |
| Replicas     | 2                                |
| Service Type | ClusterIP                        |

🎯 Real-World Scenario: A development team publishes an application image to Docker Hub, and a Kubernetes administrator deploys that image to Amazon EKS.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🎯 Learning Objectives

By the end of this lab, you will be able to:

1. Identify a custom image stored in Docker Hub.
2. Create a Kubernetes Deployment using a Docker Hub image.
3. Deploy an application with two replicas.
4. Verify image pulling and Pod creation.
5. Create a ClusterIP Service.
6. Understand how Kubernetes Services select application Pods.
7. Access the application using port forwarding.
8. Troubleshoot common image pull and deployment issues.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧰 Prerequisites

Ensure you have:

- Completed Lab 03002 — Build a Custom Docker Image & Push It to Docker Hub.
- An active EKS cluster named `vyomanant-eks`.
- Two worker nodes in `Ready` state.
- Namespace `vyomanant-lab`.
- A public Docker Hub repository named `vyomanant-web`.
- Docker Hub image tag `v1`.
- AWS CLI and kubectl configured.

### 🔎 Verify Cluster Access

```
export AWS_PROFILE=vyomanant
```

```
kubectl get nodes
```

✅ Expected: Two worker nodes display `Ready`.

Verify the namespace:

```
kubectl get namespace vyomanant-lab
```

Important: Replace `YOUR_DOCKERHUB_USERNAME` in the Deployment manifest with your actual Docker Hub username.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🏗️ Lab Architecture

```
                 Docker Hub
        ┌──────────────────────────────┐
        │ YOUR_DOCKERHUB_USERNAME      │
        │ /vyomanant-web:v1            │
        └──────────────┬───────────────┘
                       │
                       │ Image Pull
                       ▼
┌───────────────────────────────────────────────┐
│ Amazon EKS: vyomanant-eks                     │
│                                               │
│ Namespace: vyomanant-lab                      │
│                                               │
│ Deployment: vyomanant-web                     │
│                                               │
│     ┌────────────────┐ ┌────────────────┐     │
│     │ Web Pod 1      │ │ Web Pod 2      │     │
│     │ NGINX :80      │ │ NGINX :80      │     │
│     └────────┬───────┘ └───────┬────────┘     │
│              │                 │              │
│              └────────┬────────┘              │
│                       │                       │
│                       ▼                       │
│        Service: vyomanant-web-service         │
│        Type: ClusterIP                        │
│        Port: 80                               │
└───────────────────────┬───────────────────────┘
                        │
                        │ kubectl port-forward
                        ▼
                 localhost:8080
                        │
                        ▼
                 Browser / curl
```

Architecture explanation:

- EKS worker nodes pull the public image from Docker Hub.
- The Deployment maintains two application Pods.
- The ClusterIP Service selects the Pods using their labels.
- Port forwarding provides temporary access from the local machine.

Note: No public AWS load balancer is created.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🚀 Step-by-Step Instructions

## 🔹 Step 1 — Verify the Docker Hub Image

🎯 Objective: Confirm that the application image is available.

1. Open [Docker Hub](https://hub.docker.com/?utm_source=chatgpt.com).
2. Sign in to your account.
3. Open the `vyomanant-web` repository.
4. Select Tags.
5. Confirm that `v1` exists.

### Expected Image Name

```
YOUR_DOCKERHUB_USERNAME/vyomanant-web:v1
```

Important: The repository must be public for this lab. Private Docker Hub images require image pull credentials.

## 🔹 Step 2 — Create the Kubernetes Project Directory

🎯 Objective: Organize the deployment manifests.

```
mkdir vyomanant-eks-app
```

```
cd vyomanant-eks-app
```

✅ Expected: You are inside the `vyomanant-eks-app` directory.

## 🔹 Step 3 — Create the Deployment Manifest

🎯 Objective: Define the application Deployment with two replicas.

Create the file:

```
nano deployment.yaml
```

Add:

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: vyomanant-web
  namespace: vyomanant-lab
spec:
  replicas: 2
  selector:
    matchLabels:
      app: vyomanant-web
  template:
    metadata:
      labels:
        app: vyomanant-web
    spec:
      containers:
        - name: vyomanant-web
          image: YOUR_DOCKERHUB_USERNAME/vyomanant-web:v1
          ports:
            - containerPort: 80
```

Replace `YOUR_DOCKERHUB_USERNAME` before saving.

### Manifest Explanation

| Field                | Purpose                            |
| -------------------- | ---------------------------------- |
| `kind: Deployment`   | Creates a Kubernetes Deployment    |
| `namespace`          | Selects `vyomanant-lab`            |
| `replicas: 2`        | Maintains two Pods                 |
| `app: vyomanant-web` | Labels the application Pods        |
| `image`              | Specifies the Docker Hub image     |
| `containerPort: 80`  | Documents the NGINX container port |

🧠 Why It Matters: Kubernetes uses the Deployment manifest to maintain the desired application state.

## 🔹 Step 4 — Deploy the Application

🎯 Objective: Create the Deployment on EKS.

```
kubectl apply -f deployment.yaml
```

✅ Expected:

```
deployment.apps/vyomanant-web created
```

Verify:

```
kubectl get deployments -n vyomanant-lab
```

Expected (illustrative):

```
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
nginx-deployment   2/2     2            2           1h
vyomanant-web      2/2     2            2           1m
```

Note: The NGINX Deployment from Module 02 remains available.

## 🔹 Step 5 — Verify the Application Pods

🎯 Objective: Confirm that Kubernetes pulled the Docker Hub image.

```
kubectl get pods -n vyomanant-lab
```

Expected: Two `vyomanant-web` Pods reach `Running` status.

Check Pod details:

```
kubectl get pods -n vyomanant-lab -o wide
```

🔎 Verify:

- Both application Pods are running.
- Each Pod has an assigned IP address.
- Each Pod is scheduled on an EKS worker node.

Important: Both Pods may run on the same worker node, depending on Kubernetes scheduling.

## 🔹 Step 6 — Create the ClusterIP Service

🎯 Objective: Provide stable internal access to the application.

Create the file:

```
nano service.yaml
```

Add:

```
apiVersion: v1
kind: Service
metadata:
  name: vyomanant-web-service
  namespace: vyomanant-lab
spec:
  type: ClusterIP
  selector:
    app: vyomanant-web
  ports:
    - port: 80
      targetPort: 80
      protocol: TCP
```

Save the file.

Apply:

```
kubectl apply -f service.yaml
```

✅ Expected:

```
service/vyomanant-web-service created
```

Verify:

```
kubectl get services -n vyomanant-lab
```

Expected: `vyomanant-web-service` appears with type `ClusterIP`.

🧠 Why It Matters: The Service provides a stable internal endpoint even when individual Pods are replaced.

## 🔹 Step 7 — Access the Application Using Port Forwarding

🎯 Objective: Test the application running on EKS.

Run:

```
kubectl port-forward service/vyomanant-web-service 8080:80 -n vyomanant-lab
```

Expected:

```
Forwarding from 127.0.0.1:8080 -> 80
Forwarding from [::1]:8080 -> 80
```

Keep the terminal running.

Open another terminal:

```
curl http://localhost:8080
```

✅ Expected: The HTML response includes:

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

Expected: The Vyomanant Academy web page is displayed.

Stop port forwarding using Ctrl+C.

Note: If kubectl runs on a remote Ubuntu server, `localhost:8080` is accessible on that server, not automatically on your laptop.

## 🔹 Step 8 — Inspect the Deployment and Application Logs

🎯 Objective: Review application health and container logs.

Inspect the Deployment:

```
kubectl describe deployment vyomanant-web -n vyomanant-lab
```

View application logs:

```
kubectl logs deployment/vyomanant-web -n vyomanant-lab
```

✅ Expected: Deployment information and available NGINX logs are displayed.

Note: The logs command reads logs from one selected Pod rather than combining logs from all replicas.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🔎 Verification

| Check               | Expected Result         |
| ------------------- | ----------------------- |
| Docker Hub image    | `vyomanant-web:v1`      |
| EKS cluster         | Active                  |
| Namespace           | `vyomanant-lab`         |
| Deployment          | `vyomanant-web`         |
| Replicas            | 2/2 Ready               |
| Application Pods    | Running                 |
| Service             | `vyomanant-web-service` |
| Service type        | ClusterIP               |
| Local URL           | `http://localhost:8080` |
| Application version | `v1`                    |

Lab completion criteria: The custom Docker Hub image is successfully deployed to Amazon EKS, and the application is accessible through port forwarding.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🛠️ Troubleshooting

| Issue                            | Possible Cause                                     | Resolution                             |
| -------------------------------- | -------------------------------------------------- | -------------------------------------- |
| `ImagePullBackOff`               | Incorrect Docker Hub username, repository, or tag  | Verify the image name and visibility   |
| Pods remain `Pending`            | Insufficient worker node resources                 | Inspect Pod events and node capacity   |
| Deployment shows `0/2`           | Application Pods are not ready                     | Check Pod status and container logs    |
| Port forwarding fails            | Service has no ready endpoints or port is occupied | Verify Service selector and Pods       |
| Application shows the wrong page | Incorrect image or tag                             | Check the Deployment's container image |

### 🔎 Inspect Pod Events

```
kubectl describe pods -n vyomanant-lab
```

### 🔎 Inspect Service Endpoints

```
kubectl get endpointslices -n vyomanant-lab
```

Expected: An EndpointSlice associated with `vyomanant-web-service` contains the ready application Pod endpoints.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧹 Cleanup

Keep the application resources for the next lab.

In Lab 03004, we will push the same Docker image to Amazon ECR.

| Resource                            | Action |
| ----------------------------------- | ------ |
| Docker Hub image `vyomanant-web:v1` | Keep   |
| Deployment `vyomanant-web`          | Keep   |
| Service `vyomanant-web-service`     | Keep   |
| Namespace `vyomanant-lab`           | Keep   |
| EKS cluster `vyomanant-eks`         | Keep   |
| ECR repository `myapp`              | Keep   |

### Optional Cleanup

If you are not continuing the training, remove only the resources created in this lab:

```
kubectl delete -f service.yaml
```

```
kubectl delete -f deployment.yaml
```

Verify:

```
kubectl get all -n vyomanant-lab
```

Important: Do not delete the namespace or existing Module 02 application resources.

⚠️ Cost Reminder: The EKS cluster and EC2 worker nodes continue generating AWS charges.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧠 Key Takeaways

1. Amazon EKS can deploy public Docker Hub images.
2. Kubernetes Deployments manage application replicas.
3. Worker nodes pull container images from the configured registry.
4. Deployment selectors identify Pods managed by the Deployment.
5. ClusterIP Services provide stable internal application access.
6. Kubernetes Services route traffic to matching Pods.
7. Port forwarding enables temporary local access to applications.
8. Pod details and container logs help troubleshoot deployments.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 📚 Official Documentation

- [Docker Hub Documentation](https://docs.docker.com/docker-hub/?utm_source=chatgpt.com)
- [Amazon EKS Documentation](https://docs.aws.amazon.com/eks/latest/userguide/what-is-eks.html?utm_source=chatgpt.com)
- [Kubernetes Deployments](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/?utm_source=chatgpt.com)
- [Kubernetes Container Images](https://kubernetes.io/docs/concepts/containers/images/?utm_source=chatgpt.com)
- [Kubernetes Services](https://kubernetes.io/docs/concepts/services-networking/service/?utm_source=chatgpt.com)
- [Kubernetes Port Forwarding](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/?utm_source=chatgpt.com)
- [Debugging Kubernetes Pods](https://kubernetes.io/docs/tasks/debug/debug-application/debug-pods/?utm_source=chatgpt.com)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Next Lab: 03004 — Push the Same Docker Image to Amazon ECR

We will authenticate Docker to Amazon ECR, tag the existing custom application image, push it to the private ECR repository, and verify the image using the AWS Console.
