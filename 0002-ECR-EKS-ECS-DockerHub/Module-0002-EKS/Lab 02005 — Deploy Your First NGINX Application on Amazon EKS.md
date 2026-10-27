# 🧪 Lab 02005 — Deploy Your First NGINX Application on Amazon EKS

Deploy an NGINX web application on Amazon EKS using a Kubernetes Deployment, verify its Pods, and access the application through port forwarding.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🌍 Lab Overview

In this lab, you will deploy your first containerized application to the existing `vyomanant-eks` cluster.

You will create a Kubernetes Deployment with two NGINX replicas, expose them through a ClusterIP Service, and access the application locally using `kubectl port-forward`.

| Property    | Details              |
| ----------- | -------------------- |
| Lab ID      | 02005                |
| Module      | 02 — Amazon EKS      |
| Difficulty  | Beginner             |
| Duration    | 20–25 minutes        |
| Tools       | kubectl, AWS Console |
| Environment | Amazon EKS           |
| Cluster     | `vyomanant-eks`      |
| Namespace   | `vyomanant-lab`      |
| Application | NGINX                |
| Replicas    | 2                    |

Important: This lab uses the existing EKS cluster. No new cluster or external AWS load balancer is created.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🎯 Learning Objectives

By the end of this lab, you will be able to:

1. Create a Kubernetes Deployment manifest.
2. Deploy NGINX on Amazon EKS.
3. Understand Deployments, ReplicaSets, and Pods.
4. Verify application Pods and their status.
5. Create a ClusterIP Service.
6. Understand how Services route traffic to Pods.
7. Access an application using port forwarding.
8. Inspect application logs and resource details.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧰 Prerequisites

Ensure you have:

- Completed Lab 02004 — Explore EKS Cluster, Managed Nodes, Pods & Namespaces.
- An active EKS cluster named `vyomanant-eks`.
- Two worker nodes in `Ready` state.
- The namespace `vyomanant-lab`.
- AWS CLI and kubectl configured.
- Access to a terminal on your Ubuntu machine.

### 🔎 Verify Cluster Connectivity

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

✅ Expected: The namespace exists and is `Active`.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🏗️ Lab Architecture

```
              Ubuntu Linux
┌────────────────────────────────┐
│                                │
│  Browser / curl                │
│       │                        │
│       ▼                        │
│  localhost:8080                │
│       │                        │
│  kubectl port-forward          │
└───────┬────────────────────────┘
        │
        │ Kubernetes API connection
        ▼
┌──────────────────────────────────────────┐
│        Amazon EKS: vyomanant-eks         │
│                                          │
│  Namespace: vyomanant-lab                │
│                                          │
│  ┌────────────────────────────────────┐  │
│  │ Service: nginx-service             │  │
│  │ Type: ClusterIP                    │  │
│  │ Port: 80                           │  │
│  └─────────────────┬──────────────────┘  │
│                    │                     │
│          ┌─────────┴─────────┐           │
│          ▼                   ▼           │
│  ┌────────────────┐ ┌────────────────┐   │
│  │ NGINX Pod 1    │ │ NGINX Pod 2    │   │
│  │ Port 80        │ │ Port 80        │   │
│  └────────────────┘ └────────────────┘   │
│                                          │
│  Deployment: nginx-deployment            │
│  Replicas: 2                             │
└──────────────────────────────────────────┘
```

Architecture explanation:

- A Kubernetes Deployment manages two NGINX Pods.
- A ClusterIP Service provides internal access to the application.
- `kubectl port-forward` creates a temporary local connection to the Service.
- No public load balancer or public application endpoint is created.

Note: Port forwarding to a Service selects a backing Pod for the connection; it does not demonstrate load balancing across both replicas.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🚀 Step-by-Step Instructions

## 🔹 Step 1 — Create the Application Directory

🎯 Objective: Organize the Kubernetes manifest files.

```
mkdir nginx-eks-lab
```

```
cd nginx-eks-lab
```

✅ Expected: You are inside the `nginx-eks-lab` directory.

## 🔹 Step 2 — Create the NGINX Deployment Manifest

🎯 Objective: Define an NGINX Deployment with two replicas.

Create the file:

```
nano nginx-deployment.yaml
```

Add the following YAML:

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  namespace: vyomanant-lab
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: nginx:1.28
          ports:
            - containerPort: 80
```

Save the file.

### Manifest Explanation

| Field               | Purpose                                    |
| ------------------- | ------------------------------------------ |
| `kind: Deployment`  | Creates a Kubernetes Deployment            |
| `namespace`         | Specifies the application namespace        |
| `replicas: 2`       | Requests two NGINX Pods                    |
| `selector`          | Identifies Pods managed by the Deployment  |
| `image`             | Specifies the NGINX container image        |
| `containerPort: 80` | Documents the application's container port |

🧠 Why It Matters: A Deployment maintains the desired number of application Pods.

## 🔹 Step 3 — Deploy NGINX on EKS

🎯 Objective: Create the Kubernetes Deployment.

```
kubectl apply -f nginx-deployment.yaml
```

✅ Expected:

```
deployment.apps/nginx-deployment created
```

Verify the Deployment:

```
kubectl get deployments -n vyomanant-lab
```

Expected (illustrative):

```
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
nginx-deployment   2/2     2            2           1m
```

Note: The Pods may take some time to download the image and become ready.

## 🔹 Step 4 — Explore NGINX Pods

🎯 Objective: Inspect the Pods created by the Deployment.

```
kubectl get pods -n vyomanant-lab
```

Expected (illustrative):

```
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-xxxxxxxxxx-aaaaa   1/1     Running   0          2m
nginx-deployment-xxxxxxxxxx-bbbbb   1/1     Running   0          2m
```

View additional Pod information:

```
kubectl get pods -n vyomanant-lab -o wide
```

This displays the worker node and Pod IP address for each Pod.

🔎 Verify: Both Pods should be running.

🧠 Why It Matters: Kubernetes schedules Pods onto worker nodes based on available resources and scheduling rules. Both replicas are not guaranteed to run on different nodes.

## 🔹 Step 5 — Create the NGINX Service Manifest

🎯 Objective: Expose the NGINX Pods internally using a ClusterIP Service.

Create the file:

```
nano nginx-service.yaml
```

Add:

```
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
  namespace: vyomanant-lab
spec:
  type: ClusterIP
  selector:
    app: nginx
  ports:
    - port: 80
      targetPort: 80
      protocol: TCP
```

Save the file.

### Service Explanation

| Field                  | Purpose                                |
| ---------------------- | -------------------------------------- |
| `type: ClusterIP`      | Creates an internal Kubernetes Service |
| `selector: app: nginx` | Selects the NGINX Pods                 |
| `port: 80`             | Service listening port                 |
| `targetPort: 80`       | Destination port on the Pods           |

Important: A ClusterIP Service is not directly accessible from the public internet.

## 🔹 Step 6 — Deploy and Verify the Service

🎯 Objective: Create the internal Service.

```
kubectl apply -f nginx-service.yaml
```

✅ Expected:

```
service/nginx-service created
```

Verify:

```
kubectl get services -n vyomanant-lab
```

Expected (illustrative):

```
NAME            TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
nginx-service   ClusterIP   172.20.10.50    <none>        80/TCP    1m
```

Note: The actual ClusterIP will be different.

## 🔹 Step 7 — Access NGINX Using Port Forwarding

🎯 Objective: Access the application from your Ubuntu machine.

Run:

```
kubectl port-forward service/nginx-service 8080:80 -n vyomanant-lab
```

Expected:

```
Forwarding from 127.0.0.1:8080 -> 80
Forwarding from [::1]:8080 -> 80
```

Keep this terminal running.

Open another terminal and run:

```
curl http://localhost:8080
```

✅ Expected: The response contains the default NGINX welcome page HTML.

Alternatively, open the following address in a browser on the same machine:

```
http://localhost:8080
```

Expected: The NGINX welcome page is displayed.

Important: `localhost` refers to the machine running `kubectl port-forward`. If you are using a remote Ubuntu server, the URL will not automatically work from your laptop.

Stop port forwarding by pressing Ctrl+C in the first terminal.

## 🔹 Step 8 — Inspect Deployment Details and Logs

🎯 Objective: Learn basic Kubernetes application troubleshooting.

Inspect the Deployment:

```
kubectl describe deployment nginx-deployment -n vyomanant-lab
```

View application logs:

```
kubectl logs deployment/nginx-deployment -n vyomanant-lab
```

✅ Expected: Deployment details and NGINX access logs are displayed.

Note: The logs command displays logs from one selected Pod belonging to the Deployment. It does not automatically aggregate logs from all replicas.

🧠 Why It Matters: Deployment details and container logs help diagnose application startup and runtime issues.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🔎 Verification

| Check                    | Expected Result    |
| ------------------------ | ------------------ |
| EKS cluster              | Active             |
| Namespace                | `vyomanant-lab`    |
| Deployment               | `nginx-deployment` |
| Desired replicas         | 2                  |
| Available replicas       | 2                  |
| NGINX Pods               | Running            |
| Service                  | `nginx-service`    |
| Service type             | ClusterIP          |
| Local application access | Successful         |
| NGINX logs               | Accessible         |

Lab completion criteria: Two NGINX Pods are running, the ClusterIP Service exists, and the NGINX welcome page is accessible through port forwarding.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🛠️ Troubleshooting

| Issue                        | Possible Cause                                          | Resolution                                      |
| ---------------------------- | ------------------------------------------------------- | ----------------------------------------------- |
| Pods remain `Pending`        | Insufficient node resources                             | Check Pod events and node availability          |
| `ImagePullBackOff`           | Image pull failure                                      | Check image name, tag, and network connectivity |
| Deployment shows `0/2` ready | Containers are not running successfully                 | Inspect Pods and container logs                 |
| Port forwarding fails        | Service has no ready endpoints or port 8080 is occupied | Verify Pods and Service configuration           |
| `curl` connection refused    | Port forwarding stopped or wrong port                   | Restart port forwarding and retry               |

### 🔎 Check Pod Events

```
kubectl describe pods -n vyomanant-lab
```

### 🔎 Check Service Endpoints

```
kubectl get endpointslices -n vyomanant-lab
```

Expected: An EndpointSlice associated with `nginx-service` identifies the selected Pod endpoints.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧹 Cleanup

Keep the application resources for Lab 02006, where we will explore ClusterIP and LoadBalancer Services.

| Resource                      | Action |
| ----------------------------- | ------ |
| EKS cluster `vyomanant-eks`   | Keep   |
| Namespace `vyomanant-lab`     | Keep   |
| Deployment `nginx-deployment` | Keep   |
| Service `nginx-service`       | Keep   |

### Optional Cleanup

If you need to remove only the resources created in this lab:

```
kubectl delete -f nginx-service.yaml
```

```
kubectl delete -f nginx-deployment.yaml
```

Verify:

```
kubectl get all -n vyomanant-lab
```

Important: Do not delete the namespace or EKS cluster. Other training resources may depend on them.

⚠️ Cost Reminder: The EKS control plane and EC2 worker nodes continue generating AWS charges.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧠 Key Takeaways

1. Kubernetes Deployments manage application Pods.
2. ReplicaSets maintain the requested number of replicas.
3. Pods run containers on Kubernetes worker nodes.
4. Kubernetes Services provide stable access to Pods.
5. ClusterIP Services are accessible within the Kubernetes cluster.
6. `kubectl port-forward` enables temporary local application access.
7. `kubectl describe` helps inspect resource configuration and events.
8. `kubectl logs` helps troubleshoot containerized applications.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 📚 Official Documentation

- [Amazon EKS User Guide](https://docs.aws.amazon.com/eks/latest/userguide/what-is-eks.html?utm_source=chatgpt.com)
- [Kubernetes Deployments](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/?utm_source=chatgpt.com)
- [Kubernetes Pods](https://kubernetes.io/docs/concepts/workloads/pods/?utm_source=chatgpt.com)
- [Kubernetes Services](https://kubernetes.io/docs/concepts/services-networking/service/?utm_source=chatgpt.com)
- [Kubernetes Port Forwarding](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/?utm_source=chatgpt.com)
- [kubectl Logs](https://kubernetes.io/docs/reference/kubectl/generated/kubectl_logs/?utm_source=chatgpt.com)
- [kubectl Describe](https://kubernetes.io/docs/reference/kubectl/generated/kubectl_describe/?utm_source=chatgpt.com)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Next Lab: 02006 — Expose Amazon EKS Applications Using ClusterIP & LoadBalancer Services

We will compare internal and external Kubernetes Services, provision an AWS load balancer, and access the NGINX application through a public endpoint.
