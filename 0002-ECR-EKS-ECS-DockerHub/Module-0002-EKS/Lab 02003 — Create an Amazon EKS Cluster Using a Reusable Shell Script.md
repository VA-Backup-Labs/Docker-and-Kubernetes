# 🧪 Lab 02003 — Create an Amazon EKS Cluster Using a Reusable Shell Script

Create your first Amazon EKS cluster with managed EC2 worker nodes using `eksctl`, verify the cluster through `kubectl` and AWS Console, and prepare it for Kubernetes application deployment.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🌍 Lab Overview

In this lab, you will create an Amazon EKS cluster using a reusable Bash script.

Instead of entering multiple commands manually, we will use an eksctl cluster configuration file and a simple shell script to provision the infrastructure.

| Property        | Details                               |
| --------------- | ------------------------------------- |
| Lab ID          | 02003                                 |
| Module          | 02 — Amazon EKS                       |
| Difficulty      | Beginner                              |
| Duration        | 25–40 minutes                         |
| Tools           | AWS CLI, eksctl, kubectl, AWS Console |
| Environment     | Ubuntu Linux + AWS                    |
| Cluster Name    | `vyomanant-eks`                       |
| Region          | `ap-south-1`                          |
| Node Type       | `t3.medium`                           |
| Node Count      | 2                                     |
| Creation Method | Shell Script + eksctl                 |

⚠️ AWS Cost Warning: This lab creates billable AWS resources, including an EKS control plane, EC2 worker nodes, and networking resources. Keep the cluster only while performing the training labs.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🎯 Learning Objectives

By the end of this lab, you will be able to:

1. Understand the cluster configuration required by eksctl.
2. Create an EKS cluster configuration YAML file.
3. Create a reusable cluster creation shell script.
4. Provision an EKS cluster with managed node groups.
5. Configure kubectl access to the cluster.
6. Verify worker nodes and Kubernetes system Pods.
7. Explore EKS resources using AWS Console.
8. Identify cluster costs and safe cleanup requirements.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧰 Prerequisites

Ensure you have:

- Completed Lab 02002 — Install and Configure AWS CLI, kubectl & eksctl.
- AWS CLI v2, kubectl, and eksctl installed.
- An authenticated AWS CLI profile named `vyomanant`.
- IAM permissions to create EKS, EC2, VPC, IAM, and CloudFormation resources.
- Available EC2 vCPU quota for two `t3.medium` instances.
- Sufficient AWS account permissions to create EKS managed node groups.

### 🔎 Verify AWS Authentication

```
export AWS_PROFILE=vyomanant
```

```
aws sts get-caller-identity
```

✅ Expected: Your AWS account and authenticated identity are displayed.

Important: Use the same AWS account and Region throughout the remaining EKS labs.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🏗️ Lab Architecture

```
                   AWS Cloud
┌──────────────────────────────────────────────┐
│                                              │
│  Amazon EKS: vyomanant-eks                   │
│                                              │
│  ┌────────────────────────────────────────┐  │
│  │     AWS Managed Control Plane          │  │
│  │                                        │  │
│  │     Kubernetes API Server              │  │
│  │     Scheduler / Controllers / etcd     │  │
│  └──────────────────┬─────────────────────┘  │
│                     │                        │
│                     ▼                        │
│  ┌────────────────────────────────────────┐  │
│  │            Amazon VPC                  │  │
│  │                                        │  │
│  │  Public Subnet — AZ 1                  │  │
│  │  ┌──────────────────────────────────┐  │  │
│  │  │ EC2 Node 1 — t3.medium           │  │  │
│  │  │ Kubernetes Pods                  │  │  │
│  │  └──────────────────────────────────┘  │  │
│  │                                        │  │
│  │  Public Subnet — AZ 2                  │  │
│  │  ┌──────────────────────────────────┐  │  │
│  │  │ EC2 Node 2 — t3.medium           │  │  │
│  │  │ Kubernetes Pods                  │  │  │
│  │  └──────────────────────────────────┘  │  │
│  │                                        │  │
│  │  Managed Node Group: workers          │  │
│  └────────────────────────────────────────┘  │
└──────────────────────────────────────────────┘
```

Architecture explanation:

- AWS manages the Kubernetes control plane.
- eksctl creates a VPC and networking resources for the cluster.
- Two EC2 worker nodes run in public subnets across two Availability Zones.
- The managed node group is named `workers`.
- Kubernetes system Pods run on the worker nodes.

Training configuration: Public worker nodes are used to simplify networking and avoid NAT Gateway charges. This is a cost-conscious lab setup, not a recommended production architecture.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🚀 Step-by-Step Instructions

## 🔹 Step 1 — Create a Lab Directory

🎯 Objective: Organize the EKS configuration and scripts.

```
mkdir eks-lab
```

```
cd eks-lab
```

✅ Expected: You are inside the `eks-lab` directory.

## 🔹 Step 2 — Create the EKS Cluster Configuration

🎯 Objective: Define the EKS cluster and managed node group.

Create a file:

```
nano eks-cluster.yaml
```

Add the following configuration:

```
apiVersion: eksctl.io/v1alpha5
kind: ClusterConfig

metadata:
  name: vyomanant-eks
  region: ap-south-1

availabilityZones:
  - ap-south-1a
  - ap-south-1b

vpc:
  nat:
    gateway: Disable

managedNodeGroups:
  - name: workers
    instanceType: t3.medium
    desiredCapacity: 2
    minSize: 2
    maxSize: 3
    volumeSize: 20
    privateNetworking: false
    ssh:
      allow: false
```

Save the file.

### Configuration Explanation

| Setting                    | Purpose                                     |
| -------------------------- | ------------------------------------------- |
| `name`                     | EKS cluster name                            |
| `region`                   | AWS deployment Region                       |
| `availabilityZones`        | Two Availability Zones                      |
| `nat.gateway: Disable`     | Avoids creating NAT Gateways                |
| `managedNodeGroups`        | Creates AWS-managed EC2 worker nodes        |
| `instanceType`             | EC2 instance type                           |
| `desiredCapacity`          | Initial worker node count                   |
| `minSize`                  | Minimum node count                          |
| `maxSize`                  | Maximum node count                          |
| `privateNetworking: false` | Uses public subnet networking for the nodes |
| `ssh.allow: false`         | Disables SSH access configuration           |

⚠️ Important: This configuration is intended for a temporary training environment. Worker nodes in public subnets should not be treated as a production security baseline.

Technical note: The configuration omits an explicit Kubernetes version so eksctl can select a supported default. The exact version depends on the installed eksctl release and AWS EKS support at creation time.

## 🔹 Step 3 — Create the Cluster Creation Script

🎯 Objective: Automate EKS cluster provisioning.

Create the script:

```
nano eks-cluster-create.sh
```

Add:

```
#!/bin/bash

set -e

export AWS_PROFILE=vyomanant

echo "Creating Amazon EKS Cluster..."

eksctl create cluster -f eks-cluster.yaml

echo "EKS Cluster Creation Completed."
```

Save the file.

### Script Explanation

- `#!/bin/bash` identifies the Bash interpreter.
- `set -e` stops the script when a command fails.
- `AWS_PROFILE` selects the configured AWS profile.
- `eksctl create cluster` provisions the cluster using the YAML file.

🧠 Why It Matters: A reusable script makes cluster creation consistent across training sessions.

## 🔹 Step 4 — Make the Script Executable

🎯 Objective: Allow the shell script to run.

```
chmod +x eks-cluster-create.sh
```

✅ Expected: The script has executable permission.

## 🔹 Step 5 — Create the EKS Cluster

🎯 Objective: Provision the EKS cluster and managed node group.

⚠️ Before execution: Confirm the AWS account, Region, IAM permissions, EC2 quotas, and expected AWS charges.

Run:

```
./eks-cluster-create.sh
```

Expected: eksctl begins provisioning AWS infrastructure using CloudFormation.

The process typically includes:

1. Creating the EKS cluster infrastructure.
2. Provisioning the VPC and subnets.
3. Creating IAM roles and security groups.
4. Creating the EKS control plane.
5. Provisioning the managed EC2 node group.
6. Configuring local Kubernetes access.

Note: Cluster creation may take 15–30 minutes or longer depending on AWS provisioning.

⚠️ Important: Do not repeatedly run the creation script while the cluster is being provisioned.

## 🔹 Step 6 — Verify the EKS Cluster Using AWS Console

🎯 Objective: Inspect the cluster and worker nodes.

1. Open AWS Console → EKS.
2. Select Clusters.
3. Open `vyomanant-eks`.
4. Confirm the cluster status is Active.
5. Open the Compute section.
6. Locate the `workers` managed node group.
7. Confirm the node group status is Active.

### Expected Resources

| Resource           | Expected        |
| ------------------ | --------------- |
| EKS Cluster        | `vyomanant-eks` |
| Cluster Status     | Active          |
| Managed Node Group | `workers`       |
| Node Group Status  | Active          |
| EC2 Worker Nodes   | 2               |
| Instance Type      | `t3.medium`     |

✅ Expected: The EKS cluster and managed node group are active.

## 🔹 Step 7 — Configure kubectl Access

🎯 Objective: Connect kubectl to the newly created EKS cluster.

```
aws eks update-kubeconfig --region ap-south-1 --name vyomanant-eks
```

Expected: The EKS cluster context is added to your local kubeconfig file.

Note: This command updates the local kubeconfig and may change the active Kubernetes context. Confirm the active context before performing operations on other clusters.

🧠 Why It Matters: kubectl uses kubeconfig to authenticate and communicate with the EKS Kubernetes API.

## 🔹 Step 8 — Verify Kubernetes Worker Nodes

🎯 Objective: Confirm the worker nodes joined the cluster.

```
kubectl get nodes
```

✅ Expected (illustrative):

```
NAME                                           STATUS   ROLES    AGE   VERSION
ip-10-0-1-10.ap-south-1.compute.internal      Ready    <none>   5m    v1.x.x
ip-10-0-2-20.ap-south-1.compute.internal      Ready    <none>   5m    v1.x.x
```

Expected: Two worker nodes display the `Ready` status.

## 🔹 Step 9 — Verify Kubernetes System Pods

🎯 Objective: Confirm essential Kubernetes system components are running.

```
kubectl get pods -n kube-system
```

✅ Expected: System Pods such as CoreDNS, kube-proxy, and the VPC CNI components are present and healthy.

Example:

```
NAME                       READY   STATUS    RESTARTS   AGE
aws-node-xxxxx             2/2     Running   0          5m
aws-node-yyyyy             2/2     Running   0          5m
coredns-xxxxxxxxxx-aaaaa   1/1     Running   0          5m
coredns-xxxxxxxxxx-bbbbb   1/1     Running   0          5m
kube-proxy-xxxxx           1/1     Running   0          5m
kube-proxy-yyyyy           1/1     Running   0          5m
```

Note: Pod names, counts, readiness, and versions may vary.

✅ Expected: Essential Kubernetes system Pods are running.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🔎 Verification

| Check                      | Expected Result |
| -------------------------- | --------------- |
| Cluster configuration file | Created         |
| Creation script            | Executable      |
| EKS cluster                | `vyomanant-eks` |
| Cluster status             | Active          |
| Managed node group         | `workers`       |
| Worker nodes               | 2               |
| Node status                | Ready           |
| kubectl connectivity       | Successful      |
| System Pods                | Healthy         |

Lab completion criteria: Your EKS cluster is active, two worker nodes are ready, and kubectl can communicate with the Kubernetes API.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🛠️ Troubleshooting

| Issue                      | Possible Cause                            | Resolution                                                   |
| -------------------------- | ----------------------------------------- | ------------------------------------------------------------ |
| `AccessDenied`             | Missing AWS permissions                   | Verify IAM permissions for EKS, EC2, IAM, and CloudFormation |
| Cluster creation fails     | AWS quota or networking issue             | Review eksctl output and CloudFormation events               |
| Node group creation fails  | EC2 capacity or IAM configuration issue   | Check node group health in AWS Console                       |
| `kubectl` connection fails | Incorrect kubeconfig or authentication    | Update kubeconfig and verify AWS identity                    |
| Nodes show `NotReady`      | Node networking or system component issue | Inspect node details and kube-system Pods                    |

### 🔎 Check Cluster Details

```
eksctl get cluster --region ap-south-1
```

### 🔎 Inspect Worker Node Problems

```
kubectl describe nodes
```

### 🔎 Check Kubernetes System Pods

```
kubectl get pods -n kube-system
```

Additional troubleshooting: Open AWS Console → CloudFormation → Stacks to review any failed provisioning events.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧹 Cleanup

Do not delete the EKS cluster yet.

The `vyomanant-eks` cluster will be reused in the remaining EKS labs and the ECR + Docker Hub + GitHub Actions integration module.

### Planned Cleanup

A reusable deletion script will be introduced in Lab 02009.

The script will delete the training cluster and its eksctl-managed infrastructure.

⚠️ Cost Warning: The EKS cluster and EC2 worker nodes continue generating charges while running. If you cannot continue the training soon, delete the cluster using the documented eksctl cleanup procedure rather than leaving resources running indefinitely.

Important: Before final cluster deletion, remove any Kubernetes-created external AWS resources, such as load balancers, to avoid orphaned infrastructure.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧠 Key Takeaways

1. eksctl simplifies Amazon EKS cluster provisioning.
2. YAML configuration files define cluster infrastructure consistently.
3. Shell scripts make cluster creation repeatable.
4. AWS manages the EKS Kubernetes control plane.
5. Managed node groups provision EC2 worker nodes.
6. kubectl communicates with the Kubernetes API using kubeconfig.
7. Worker nodes must be `Ready` before deploying applications.
8. EKS infrastructure incurs charges and requires planned cleanup.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 📚 Official Documentation

- [Amazon EKS Getting Started with eksctl](https://docs.aws.amazon.com/eks/latest/userguide/getting-started-eksctl.html?utm_source=chatgpt.com)
- [eksctl Cluster Configuration](https://eksctl.io/usage/creating-and-managing-clusters/?utm_source=chatgpt.com)
- [Amazon EKS Managed Node Groups](https://docs.aws.amazon.com/eks/latest/userguide/managed-node-groups.html?utm_source=chatgpt.com)
- [Configure kubectl for EKS](https://docs.aws.amazon.com/eks/latest/userguide/create-kubeconfig.html?utm_source=chatgpt.com)
- [Amazon EKS Networking](https://docs.aws.amazon.com/eks/latest/userguide/eks-networking.html?utm_source=chatgpt.com)
- [Amazon EKS Pricing](https://aws.amazon.com/eks/pricing/?utm_source=chatgpt.com)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Next Lab: 02004 — Explore Amazon EKS Cluster, Managed Nodes, Pods & Namespaces

We will explore the newly created EKS cluster using the AWS Console and essential kubectl commands before deploying our first application.
