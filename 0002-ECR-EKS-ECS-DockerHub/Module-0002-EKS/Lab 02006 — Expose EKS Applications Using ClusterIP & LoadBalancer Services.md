# 🧪 Lab 02006 — Expose EKS Applications Using ClusterIP & LoadBalancer Services

Expose an NGINX application internally through a ClusterIP Service and externally through an AWS Load Balancer, using Kubernetes YAML and the AWS Console.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🌍 Lab Overview

In the previous lab, we deployed NGINX with two replicas and created an internal ClusterIP Service.

In this lab, we will explore how Kubernetes Services provide network access to applications and create a LoadBalancer Service to make NGINX accessible from the internet.

| Property      | Details                 |
| ------------- | ----------------------- |
| Lab ID        | 02006                   |
| Module        | 02 — Amazon EKS         |
| Difficulty    | Beginner                |
| Duration      | 20–30 minutes           |
| Tools         | kubectl, AWS Console    |
| Environment   | Amazon EKS              |
| Cluster       | `vyomanant-eks`         |
| Namespace     | `vyomanant-lab`         |
| Application   | NGINX                   |
| Service Types | ClusterIP, LoadBalancer |

⚠️ AWS Cost Warning: Creating a Kubernetes LoadBalancer Service provisions billable AWS infrastructure. We will delete the external Service after testing.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🎯 Learning Objectives

By the end of this lab, you will be able to:

1. Explain Kubernetes Service types.
2. Identify an existing ClusterIP Service.
3. Understand internal and external application access.
4. Create a LoadBalancer Service using YAML.
5. Verify AWS load balancer provisioning.
6. Access NGINX through a public endpoint.
7. Inspect Service configuration and endpoints.
8. Safely remove an external LoadBalancer Service.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧰 Prerequisites

Ensure you have:

- Completed Lab 02005 — Deploy Your First NGINX Application on Amazon EKS.
- An active EKS cluster named `vyomanant-eks`.
- Two worker nodes in `Ready` state.
- Namespace `vyomanant-lab`.
- Deployment `nginx-deployment` with two replicas.
- Existing ClusterIP Service `nginx-service`.
- kubectl configured for the EKS cluster.
- AWS Console permissions to view EC2 load balancers.

### 🔎 Verify Existing Resources

```
export AWS_PROFILE=vyomanant
```

```
kubectl get deployments -n vyomanant-lab
```

```
kubectl get services -n vyomanant-lab
```

✅ Expected: `nginx-deployment` and `nginx-service` exist.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🏗️ Lab Architecture

```
                  Internet
                     │
                     │ HTTP :80
                     ▼
       ┌─────────────────────────┐
       │ AWS Network Load        │
       │ Balancer                │
       └────────────┬────────────┘
                    │
                    ▼
┌────────────────────────────────────────────┐
│ Amazon EKS: vyomanant-eks                  │
│                                            │
│ Namespace: vyomanant-lab                   │
│                                            │
│  ┌──────────────────────────────────────┐  │
│  │ Service: nginx-loadbalancer          │  │
│  │ Type: LoadBalancer                   │  │
│  │ Port: 80                             │  │
│  └──────────────────┬───────────────────┘  │
│                     │                      │
│          ┌──────────┴──────────┐           │
│          ▼                     ▼           │
│  ┌──────────────┐      ┌──────────────┐    │
│  │ NGINX Pod 1  │      │ NGINX Pod 2  │    │
│  │ Port 80      │      │ Port 80      │    │
│  └──────────────┘      └──────────────┘    │
│          ▲                     ▲           │
│          └──────────┬──────────┘           │
│                     │                      │
│  ┌──────────────────┴───────────────────┐  │
│  │ Service: nginx-service               │  │
│  │ Type: ClusterIP                      │  │
│  │ Internal Port: 80                    │  │
│  └──────────────────────────────────────┘  │
│                                            │
└────────────────────────────────────────────┘
```

Architecture explanation:

- `nginx-service` provides internal cluster access.
- `nginx-loadbalancer` provides external access through an AWS Network Load Balancer.
- Both Services select the same NGINX Pods using the label `app: nginx`.
- The AWS load balancer forwards traffic through the EKS worker nodes to the application Pods.

Note: The architecture assumes the EKS Auto Mode load balancing capability is not enabled and uses the legacy AWS cloud provider integration for Network Load Balancer provisioning. The manifest below explicitly requests an NLB for the managed EC2 node group created in Lab 02003.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🚀 Step-by-Step Instructions

## 🔹 Step 1 — Verify the Existing ClusterIP Service

🎯 Objective: Inspect the internal NGINX Service.

```
kubectl get services -n vyomanant-lab
```

✅ Expected (illustrative):

```
NAME            TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
nginx-service   ClusterIP   172.20.10.50    <none>        80/TCP    10m
```

🧠 Why It Matters: ClusterIP Services provide stable internal networking for Kubernetes applications.

## 🔹 Step 2 — Understand Kubernetes Service Types

🎯 Objective: Compare common Kubernetes Service types.

| Service Type | Purpose                                     | External Access                       |
| ------------ | ------------------------------------------- | ------------------------------------- |
| ClusterIP    | Internal application communication          | No                                    |
| NodePort     | Exposes a Service on a port of each node    | Possible, depending on network access |
| LoadBalancer | Requests an external load balancer          | Yes, when publicly configured         |
| ExternalName | Maps a Service name to an external DNS name | DNS alias only                        |

For this lab: We will use `LoadBalancer` to expose NGINX through AWS.

## 🔹 Step 3 — Create the LoadBalancer Service Manifest

🎯 Objective: Define a public LoadBalancer Service.

Navigate to the directory created in Lab 02005:

```
cd nginx-eks-lab
```

Create the manifest:

```
nano nginx-loadbalancer.yaml
```

Add:

```
apiVersion: v1
kind: Service
metadata:
  name: nginx-loadbalancer
  namespace: vyomanant-lab
  annotations:
    service.beta.kubernetes.io/aws-load-balancer-type: "nlb"
    service.beta.kubernetes.io/aws-load-balancer-scheme: "internet-facing"
spec:
  type: LoadBalancer
  selector:
    app: nginx
  ports:
    - port: 80
      targetPort: 80
      protocol: TCP
```

### Manifest Explanation

| Field                           | Purpose                          |
| ------------------------------- | -------------------------------- |
| `type: LoadBalancer`            | Requests an AWS load balancer    |
| `aws-load-balancer-type: "nlb"` | Requests a Network Load Balancer |
| `aws-load-balancer-scheme`      | Requests internet-facing access  |
| `selector: app: nginx`          | Selects NGINX Pods               |
| `port: 80`                      | Load balancer listener port      |
| `targetPort: 80`                | NGINX container destination port |

⚠️ Security Warning: This configuration exposes the NGINX welcome page publicly over HTTP. It is intended only for temporary training. Production applications should use appropriate TLS, network restrictions, and security controls.

Technical Note: AWS load balancer provisioning behavior depends on the EKS cluster configuration and installed controllers. For this lab's standard eksctl-managed cluster, the NLB annotation is intended for the legacy AWS cloud provider integration. Clusters using AWS Load Balancer Controller or EKS Auto Mode require their corresponding supported configuration.

## 🔹 Step 4 — Create the LoadBalancer Service

🎯 Objective: Provision an AWS load balancer.

```
kubectl apply -f nginx-loadbalancer.yaml
```

✅ Expected:

```
service/nginx-loadbalancer created
```

Verify:

```
kubectl get services -n vyomanant-lab
```

Expected (initially):

```
NAME                 TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)
nginx-service        ClusterIP      172.20.10.50     <none>        80/TCP
nginx-loadbalancer   LoadBalancer   172.20.20.100    <pending>     80:3xxxx/TCP
```

Note: AWS load balancer provisioning can take several minutes. The external address will appear after provisioning succeeds.

## 🔹 Step 5 — Verify the AWS Load Balancer

🎯 Objective: Inspect the infrastructure created by Kubernetes.

1. Open the AWS Management Console.
2. Navigate to EC2 → Load Balancers.
3. Locate the load balancer associated with `nginx-loadbalancer`.
4. Confirm its type and scheme.

| Property     | Expected                         |
| ------------ | -------------------------------- |
| Type         | Network Load Balancer            |
| Scheme       | Internet-facing                  |
| Listener     | TCP:80                           |
| State        | Active                           |
| Target group | Associated with EKS worker nodes |

✅ Expected: An AWS Network Load Balancer is created and becomes active.

Important: An active load balancer does not automatically mean the application is healthy. Check the target group's health status as well.

## 🔹 Step 6 — Access NGINX Through the Public Endpoint

🎯 Objective: Test application access from outside the Kubernetes cluster.

Run:

```
kubectl get services -n vyomanant-lab
```

Find the external DNS name of `nginx-loadbalancer`.

Expected (illustrative):

```
nginx-loadbalancer   LoadBalancer   172.20.20.100   abc123.elb.ap-south-1.amazonaws.com   80:3xxxx/TCP
```

Open the following URL in your browser, replacing the example DNS name with your actual load balancer address:

```
http://abc123.elb.ap-south-1.amazonaws.com
```

✅ Expected: The default Welcome to nginx! page is displayed.

Note: If the page does not load immediately, wait for the load balancer targets to become healthy and retry.

## 🔹 Step 7 — Inspect the LoadBalancer Service

🎯 Objective: Examine the Kubernetes Service configuration and events.

```
kubectl describe service nginx-loadbalancer -n vyomanant-lab
```

Review:

- Service type.
- External address.
- Service port.
- NodePort.
- Selected Pod endpoints.
- Kubernetes events.

🧠 Why It Matters: The `describe` command helps troubleshoot cloud load balancer provisioning and Service routing problems.

## 🔹 Step 8 — Compare Internal and External Access

🎯 Objective: Understand how both Services access the same application.

```
kubectl get services -n vyomanant-lab
```

| Feature             | `nginx-service`            | `nginx-loadbalancer` |
| ------------------- | -------------------------- | -------------------- |
| Service Type        | ClusterIP                  | LoadBalancer         |
| Access              | Internal                   | Public               |
| External DNS        | No                         | Yes                  |
| AWS Load Balancer   | No                         | Yes                  |
| Backend Pods        | NGINX Pods                 | Same NGINX Pods      |
| Additional AWS Cost | No dedicated load balancer | Yes                  |

✅ Expected: Both Services exist and select the same application Pods.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🔎 Verification

| Check                | Expected Result    |
| -------------------- | ------------------ |
| NGINX Deployment     | 2/2 Ready          |
| ClusterIP Service    | Available          |
| LoadBalancer Service | Created            |
| AWS Load Balancer    | Active             |
| Load Balancer Type   | Network            |
| Target Group         | Healthy targets    |
| Public DNS           | Assigned           |
| Browser Access       | NGINX welcome page |

Lab completion criteria: The NGINX application is accessible through the public AWS load balancer endpoint.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🛠️ Troubleshooting

| Issue                           | Possible Cause                                      | Resolution                                     |
| ------------------------------- | --------------------------------------------------- | ---------------------------------------------- |
| External IP remains `<pending>` | Load balancer provisioning failed                   | Check Service events and AWS permissions       |
| Load balancer not created       | Unsupported annotations or controller configuration | Verify the cluster's load balancer integration |
| Public URL does not respond     | Targets unhealthy or security group restrictions    | Inspect target health and network rules        |
| NGINX page unavailable          | Application Pods not ready                          | Check Deployment and Pod status                |
| Access denied in AWS Console    | Missing EC2 read permissions                        | Request appropriate IAM permissions            |

### 🔎 Inspect Service Events

```
kubectl describe service nginx-loadbalancer -n vyomanant-lab
```

### 🔎 Verify Application Pods

```
kubectl get pods -n vyomanant-lab
```

Technical Note: If the cluster uses AWS Load Balancer Controller or EKS Auto Mode, use the Service configuration documented for that integration rather than mixing controller-specific annotations.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧹 Cleanup

🎯 Objective: Remove the public load balancer to avoid unnecessary AWS charges.

Delete only the LoadBalancer Service created in this lab:

```
kubectl delete -f nginx-loadbalancer.yaml
```

✅ Expected:

```
service "nginx-loadbalancer" deleted
```

Verify the remaining Services:

```
kubectl get services -n vyomanant-lab
```

Expected:

```
NAME            TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)
nginx-service   ClusterIP   172.20.10.50    <none>        80/TCP
```

### AWS Console Verification

1. Open EC2 → Load Balancers.
2. Confirm the training load balancer is being deleted.
3. Wait until it disappears.
4. Check that its associated target group is also removed.

Important: Do not manually delete the load balancer before deleting its Kubernetes Service. Kubernetes should manage the lifecycle of the infrastructure it creates.

### Resources to Keep

| Resource                                  | Action |
| ----------------------------------------- | ------ |
| EKS cluster `vyomanant-eks`               | Keep   |
| Managed node group `workers`              | Keep   |
| Namespace `vyomanant-lab`                 | Keep   |
| Deployment `nginx-deployment`             | Keep   |
| ClusterIP Service `nginx-service`         | Keep   |
| LoadBalancer Service `nginx-loadbalancer` | Delete |

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧠 Key Takeaways

1. ClusterIP Services provide internal Kubernetes application access.
2. LoadBalancer Services can expose applications externally.
3. Amazon EKS integrates with AWS load balancing services.
4. Kubernetes Service selectors identify backend Pods.
5. AWS Network Load Balancers can distribute traffic to EKS workloads.
6. Service events help troubleshoot load balancer provisioning.
7. Public endpoints require appropriate security controls.
8. Deleting unused LoadBalancer Services helps control AWS costs.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 📚 Official Documentation

- [Kubernetes Services](https://kubernetes.io/docs/concepts/services-networking/service/?utm_source=chatgpt.com)
- [Kubernetes Service Type LoadBalancer](https://kubernetes.io/docs/concepts/services-networking/service/?utm_source=chatgpt.com#loadbalancer)
- [Amazon EKS Load Balancing](https://docs.aws.amazon.com/eks/latest/userguide/network-load-balancing.html?utm_source=chatgpt.com)
- [AWS Network Load Balancers](https://docs.aws.amazon.com/elasticloadbalancing/latest/network/introduction.html?utm_source=chatgpt.com)
- [AWS Load Balancer Controller](https://kubernetes-sigs.github.io/aws-load-balancer-controller/latest/?utm_source=chatgpt.com)
- [Amazon EKS Auto Mode Load Balancing](https://docs.aws.amazon.com/eks/latest/userguide/auto-configure-nlb.html?utm_source=chatgpt.com)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Next Lab: 02007 — Perform Rolling Updates, Rollbacks & Application Scaling on Amazon EKS

We will update the NGINX container image, observe Kubernetes rolling updates, perform a rollback, and scale the application replicas using simple `kubectl` commands.
