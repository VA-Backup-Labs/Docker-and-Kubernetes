# 🧪 Lab 02004 — Explore Amazon EKS Cluster, Managed Nodes, Pods & Namespaces

Explore your existing Amazon EKS cluster using the AWS Management Console and essential `kubectl` commands to understand how Kubernetes resources are organized.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🌍 Lab Overview

In the previous lab, we created the `vyomanant-eks` cluster with two managed EC2 worker nodes.

In this lab, you will inspect the cluster, explore its managed node group, view Kubernetes system Pods, and understand namespaces.

No new AWS infrastructure will be created.

| Property    | Details                       |
| ----------- | ----------------------------- |
| Lab ID      | 02004                         |
| Module      | 02 — Amazon EKS               |
| Difficulty  | Beginner                      |
| Duration    | 20–25 minutes                 |
| Tools       | AWS Console, kubectl          |
| Environment | Amazon EKS                    |
| Cluster     | `vyomanant-eks`               |
| Region      | `ap-south-1`                  |
| Approach    | Console-first + Essential CLI |

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🎯 Learning Objectives

By the end of this lab, you will be able to:

1. Explore an EKS cluster using the AWS Console.
2. Identify managed node groups and EC2 worker nodes.
3. Verify Kubernetes cluster connectivity.
4. List and inspect Kubernetes nodes.
5. Explore Kubernetes namespaces.
6. Identify Kubernetes system Pods.
7. Understand the relationship between nodes, namespaces, and Pods.
8. Use basic `kubectl` commands for cluster inspection.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧰 Prerequisites

Ensure you have:

- Completed Lab 02003 — Create an Amazon EKS Cluster Using a Reusable Shell Script.
- An active EKS cluster named `vyomanant-eks`.
- Two managed EC2 worker nodes in `Ready` state.
- AWS CLI, kubectl, and eksctl installed.
- AWS Console access.
- A working AWS CLI profile named `vyomanant`.

### 🔎 Verify AWS Authentication

```
export AWS_PROFILE=vyomanant
```

```
aws sts get-caller-identity
```

✅ Expected: Your authenticated AWS identity is displayed.

Note: If your IAM Identity Center session has expired, sign in again using the authentication method configured in Lab 02002.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🏗️ Lab Architecture

```
                  AWS Cloud
┌──────────────────────────────────────────┐
│                                          │
│      Amazon EKS: vyomanant-eks           │
│                                          │
│  ┌────────────────────────────────────┐  │
│  │     AWS Managed Control Plane      │  │
│  │                                    │  │
│  │     Kubernetes API Server          │  │
│  └─────────────────┬──────────────────┘  │
│                    │                     │
│                    ▼                     │
│  ┌────────────────────────────────────┐  │
│  │        Managed Node Group          │  │
│  │             workers                │  │
│  │                                    │  │
│  │  ┌────────────┐  ┌────────────┐    │  │
│  │  │ EC2 Node 1 │  │ EC2 Node 2 │    │  │
│  │  │ t3.medium  │  │ t3.medium  │    │  │
│  │  │            │  │            │    │  │
│  │  │ System Pods│  │ System Pods│    │  │
│  │  └────────────┘  └────────────┘    │  │
│  └────────────────────────────────────┘  │
│                                          │
│  Kubernetes Namespaces                   │
│  ├── default                             │
│  ├── kube-system                         │
│  ├── kube-public                         │
│  └── kube-node-lease                     │
│                                          │
└──────────────────────────────────────────┘
```

Architecture explanation:

- The EKS control plane manages Kubernetes resources.
- The `workers` managed node group contains two EC2 worker nodes.
- System Pods run on the worker nodes.
- Namespaces logically organize Kubernetes resources; they are not separate EC2 machines.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🚀 Step-by-Step Instructions

## 🔹 Step 1 — Explore the EKS Cluster Using AWS Console

🎯 Objective: Inspect the cluster configuration.

1. Sign in to the [AWS Management Console](https://console.aws.amazon.com/?utm_source=chatgpt.com).
2. Navigate to Amazon EKS → Clusters.
3. Select `vyomanant-eks`.
4. Open the cluster overview.

Review the following information:

| Property           | Expected                         |
| ------------------ | -------------------------------- |
| Cluster name       | `vyomanant-eks`                  |
| Cluster status     | Active                           |
| AWS Region         | `ap-south-1`                     |
| Kubernetes version | Version selected during creation |
| Compute            | Managed node group               |
| Node group         | `workers`                        |

✅ Expected: The cluster status is Active.

🧠 Why It Matters: The EKS Console provides centralized visibility into cluster infrastructure and configuration.

## 🔹 Step 2 — Explore the Managed Node Group

🎯 Objective: Understand the EC2 infrastructure running Kubernetes workloads.

1. Open the cluster's Compute tab.
2. Locate the managed node group named `workers`.
3. Select the node group.
4. Review its configuration.

| Setting           | Expected    |
| ----------------- | ----------- |
| Node group        | `workers`   |
| Instance type     | `t3.medium` |
| Desired nodes     | 2           |
| Minimum nodes     | 2           |
| Maximum nodes     | 3           |
| Node group status | Active      |

Next, open AWS Console → EC2 → Instances.

Locate the EC2 instances associated with the EKS managed node group.

✅ Expected: Two running EC2 instances are visible.

Note: The number of nodes can temporarily differ during updates or scaling operations.

## 🔹 Step 3 — Verify kubectl Cluster Connectivity

🎯 Objective: Confirm that kubectl is connected to the correct EKS cluster.

```
kubectl config current-context
```

✅ Expected: The active context references `vyomanant-eks`.

Example:

```
arn:aws:eks:ap-south-1:123456789012:cluster/vyomanant-eks
```

If the context is missing, update kubeconfig:

```
aws eks update-kubeconfig --region ap-south-1 --name vyomanant-eks
```

🧠 Why It Matters: Always verify your active Kubernetes context before running commands, especially when working with multiple clusters.

## 🔹 Step 4 — Explore Kubernetes Worker Nodes

🎯 Objective: Inspect the nodes registered with Kubernetes.

List nodes:

```
kubectl get nodes
```

✅ Expected (illustrative):

```
NAME                                         STATUS   ROLES    AGE   VERSION
ip-10-0-1-10.ap-south-1.compute.internal    Ready    <none>   20m   v1.x.x
ip-10-0-2-20.ap-south-1.compute.internal    Ready    <none>   20m   v1.x.x
```

View additional node information:

```
kubectl get nodes -o wide
```

This displays information such as internal IP addresses, operating systems, and container runtimes.

🔎 Verify: Both nodes should show `Ready`.

🧠 Why It Matters: Kubernetes schedules application Pods onto available worker nodes.

## 🔹 Step 5 — Explore Kubernetes Namespaces

🎯 Objective: Understand how Kubernetes organizes resources.

List namespaces:

```
kubectl get namespaces
```

✅ Expected (illustrative):

```
NAME              STATUS   AGE
default           Active   25m
kube-node-lease   Active   25m
kube-public       Active   25m
kube-system       Active   25m
```

### Default Kubernetes Namespaces

| Namespace         | Purpose                                         |
| ----------------- | ----------------------------------------------- |
| `default`         | General workloads without a specified namespace |
| `kube-system`     | Kubernetes system components                    |
| `kube-public`     | Publicly readable cluster information           |
| `kube-node-lease` | Node heartbeat lease objects                    |

Important: Namespaces provide logical separation within a Kubernetes cluster. They do not automatically provide complete security or network isolation.

## 🔹 Step 6 — Explore Kubernetes System Pods

🎯 Objective: Identify the Pods supporting the EKS cluster.

```
kubectl get pods -n kube-system
```

✅ Expected (illustrative):

```
NAME                       READY   STATUS    RESTARTS   AGE
aws-node-xxxxx             2/2     Running   0          20m
aws-node-yyyyy             2/2     Running   0          20m
coredns-xxxxxxxxxx-aaaaa   1/1     Running   0          20m
coredns-xxxxxxxxxx-bbbbb   1/1     Running   0          20m
kube-proxy-xxxxx           1/1     Running   0          20m
kube-proxy-yyyyy           1/1     Running   0          20m
```

### Important System Components

| Component    | Purpose                           |
| ------------ | --------------------------------- |
| `aws-node`   | Amazon VPC CNI networking         |
| `coredns`    | DNS resolution inside the cluster |
| `kube-proxy` | Kubernetes Service networking     |

Note: Pod names, counts, and readiness values depend on the EKS version and configuration.

🧠 Why It Matters: These components support networking and service discovery for Kubernetes applications.

## 🔹 Step 7 — Create a Dedicated Lab Namespace

🎯 Objective: Create a namespace for upcoming application deployments.

Instead of deploying applications into the `default` namespace, we will use a dedicated namespace named `vyomanant-lab`.

```
kubectl create namespace vyomanant-lab
```

✅ Expected:

```
namespace/vyomanant-lab created
```

Verify:

```
kubectl get namespaces
```

✅ Expected: The `vyomanant-lab` namespace appears with status `Active`.

🧠 Why It Matters: Dedicated namespaces help organize training workloads and simplify resource management.

Important: Keep this namespace for the next EKS application deployment labs.

## 🔹 Step 8 — Explore Resources Across Namespaces

🎯 Objective: Understand how Kubernetes displays resources from different namespaces.

List Pods in the lab namespace:

```
kubectl get pods -n vyomanant-lab
```

✅ Expected:

```
No resources found in vyomanant-lab namespace.
```

List Pods across all namespaces:

```
kubectl get pods -A
```

The `-A` flag means all namespaces.

✅ Expected: System Pods are displayed along with their namespaces. The new lab namespace has no Pods yet.

## 🔹 Step 9 — Inspect Cluster Events

🎯 Objective: Learn how to view Kubernetes events for troubleshooting.

```
kubectl get events -n kube-system
```

✅ Expected: Recent system events are displayed, or Kubernetes reports that no events are currently available.

Events can help identify issues related to:

- Pod scheduling.
- Container startup.
- Image pulling.
- Node readiness.

🧠 Why It Matters: Kubernetes events are often the first place to investigate workload problems.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🔎 Verification

| Verification       | Expected Result |
| ------------------ | --------------- |
| EKS cluster        | Active          |
| Managed node group | `workers`       |
| Worker nodes       | 2 Ready nodes   |
| kubectl context    | `vyomanant-eks` |
| System Pods        | Healthy         |
| Default namespaces | Available       |
| Lab namespace      | `vyomanant-lab` |
| Lab namespace Pods | None            |
| Cluster events     | Accessible      |

Lab completion criteria: You can inspect EKS infrastructure, explore Kubernetes resources, and confirm the `vyomanant-lab` namespace is ready for application deployment.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🛠️ Troubleshooting

| Issue                        | Possible Cause                           | Resolution                                   |
| ---------------------------- | ---------------------------------------- | -------------------------------------------- |
| `kubectl` connection refused | Incorrect kubeconfig or cluster endpoint | Update kubeconfig and verify the cluster     |
| `Unauthorized`               | Missing EKS access permissions           | Review EKS access entries and IAM identity   |
| Nodes show `NotReady`        | Node or networking issues                | Inspect node conditions and system Pods      |
| System Pods show `Pending`   | Scheduling or resource issue             | Describe the affected Pod                    |
| Namespace already exists     | Created previously                       | Reuse the existing `vyomanant-lab` namespace |

### 🔎 Inspect Node Conditions

```
kubectl describe nodes
```

### 🔎 Check System Pod Status

```
kubectl get pods -n kube-system
```

### 🔎 Check Namespace Details

```
kubectl describe namespace vyomanant-lab
```

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧹 Cleanup

Do not delete the EKS cluster or the `vyomanant-lab` namespace.

They will be reused in upcoming labs.

| Resource                     | Action |
| ---------------------------- | ------ |
| EKS cluster `vyomanant-eks`  | Keep   |
| Managed node group `workers` | Keep   |
| Namespace `vyomanant-lab`    | Keep   |

⚠️ Cost Reminder: The EKS cluster and EC2 worker nodes continue to incur AWS charges while running.

If you are ending the training, use the cluster deletion procedure introduced later rather than leaving unused infrastructure running.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧠 Key Takeaways

1. AWS Console provides visibility into EKS cluster infrastructure.
2. Managed node groups provision EC2 worker nodes.
3. `kubectl get nodes` displays Kubernetes worker nodes.
4. Nodes must be `Ready` to reliably run application workloads.
5. Kubernetes namespaces organize resources logically.
6. The `kube-system` namespace contains important cluster components.
7. `kubectl get pods -A` displays Pods across all namespaces.
8. Kubernetes events help troubleshoot cluster and application issues.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 📚 Official Documentation

- [Amazon EKS User Guide](https://docs.aws.amazon.com/eks/latest/userguide/what-is-eks.html?utm_source=chatgpt.com)
- [Amazon EKS Managed Node Groups](https://docs.aws.amazon.com/eks/latest/userguide/managed-node-groups.html?utm_source=chatgpt.com)
- [Kubernetes Nodes](https://kubernetes.io/docs/concepts/architecture/nodes/?utm_source=chatgpt.com)
- [Kubernetes Namespaces](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/?utm_source=chatgpt.com)
- [Kubernetes Pods](https://kubernetes.io/docs/concepts/workloads/pods/?utm_source=chatgpt.com)
- [kubectl Command Reference](https://kubernetes.io/docs/reference/kubectl/?utm_source=chatgpt.com)
- [Kubernetes Events](https://kubernetes.io/docs/reference/kubernetes-api/cluster-resources/event-v1/?utm_source=chatgpt.com)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Next Lab: 02005 — Deploy Your First NGINX Application on Amazon EKS

In the next lab, we will deploy NGINX into the `vyomanant-lab` namespace using a Kubernetes Deployment, verify the Pods, and access the application using port forwarding.
