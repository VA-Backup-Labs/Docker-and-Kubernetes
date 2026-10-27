# 🧪 Lab 02001 — Amazon EKS Architecture, IAM, VPC & Cluster Prerequisites

Understand Amazon EKS architecture, explore its core AWS components, and prepare your AWS environment before creating your first Kubernetes cluster.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🌍 Lab Overview

Amazon Elastic Kubernetes Service (EKS) is a managed Kubernetes service that simplifies running Kubernetes clusters on AWS.

AWS manages the Kubernetes control plane, while your applications run on worker nodes such as Amazon EC2 instances.

In this lab, you will explore the EKS service using the AWS Management Console, understand the required IAM and networking components, and prepare for cluster creation in upcoming labs.

| Property              | Details                |
| --------------------- | ---------------------- |
| Lab ID                | 02001                  |
| Module                | 02 — Amazon EKS        |
| Difficulty            | Beginner               |
| Duration              | 20–25 minutes          |
| Tools                 | AWS Management Console |
| Environment           | AWS Account            |
| Approach              | Console Only           |
| AWS Resources Created | None                   |

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🎯 Learning Objectives

By the end of this lab, you will be able to:

1. Explain the purpose of Amazon EKS.
2. Identify the Kubernetes control plane and worker nodes.
3. Understand EKS managed node groups.
4. Identify the role of VPCs, subnets, and security groups.
5. Understand IAM permissions required for EKS.
6. Navigate the EKS Console.
7. Identify EKS cluster creation prerequisites.
8. Understand basic EKS cost considerations.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧰 Prerequisites

Ensure you have:

- An active AWS account.
- AWS Management Console access.
- IAM permissions to view EKS, EC2, VPC, and IAM resources.
- Basic understanding of Docker containers.
- Basic knowledge of Kubernetes Pods, Deployments, and Services.
- Completed Module 01 — Amazon ECR.
- A selected AWS Region for the training.

Recommended: Continue using the same AWS Region selected during the ECR labs.

⚠️ Important: Do not create an EKS cluster in this lab. Cluster creation will be performed using a reusable shell script in Lab 02003.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🏗️ Lab Architecture

```
                 AWS Cloud
┌───────────────────────────────────────────────┐
│                                               │
│        Amazon EKS Cluster                     │
│                                               │
│  ┌─────────────────────────────────────────┐  │
│  │       AWS Managed Control Plane         │  │
│  │                                         │  │
│  │  Kubernetes API Server                  │  │
│  │  Scheduler                              │  │
│  │  Controller Manager                     │  │
│  │  etcd                                   │  │
│  └───────────────────┬─────────────────────┘  │
│                      │                        │
│                      │ Kubernetes API         │
│                      ▼                        │
│  ┌─────────────────────────────────────────┐  │
│  │               Amazon VPC                │  │
│  │                                         │  │
│  │  ┌───────────────────────────────────┐  │  │
│  │  │       Managed Node Group          │  │  │
│  │  │                                   │  │  │
│  │  │  EC2 Node 1      EC2 Node 2       │  │  │
│  │  │  ┌────────┐      ┌────────┐       │  │  │
│  │  │  │ Pods   │      │ Pods   │       │  │  │
│  │  │  └────────┘      └────────┘       │  │  │
│  │  └───────────────────────────────────┘  │  │
│  └─────────────────────────────────────────┘  │
│                                               │
└───────────────────────────────────────────────┘
```

Architecture explanation:

- AWS-managed control plane: Runs Kubernetes management components.
- Amazon VPC: Provides networking for EKS worker nodes and workloads.
- Managed node group: Manages EC2 worker node provisioning and lifecycle.
- EC2 worker nodes: Run Kubernetes Pods and application containers.

Note: This is a conceptual architecture. No EKS resources are created in this lab. The control plane is managed by AWS and is not deployed as ordinary EC2 instances in your VPC.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🚀 Step-by-Step Instructions

## 🔹 Step 1 — Open Amazon EKS Console

🎯 Objective: Explore the Amazon EKS service.

1. Sign in to the [AWS Management Console](https://console.aws.amazon.com/?utm_source=chatgpt.com).
2. Search for EKS.
3. Select Elastic Kubernetes Service.
4. Open Clusters.
5. Confirm your selected AWS Region.

🔎 Verify: The EKS cluster list is displayed.

✅ Expected: You can access the EKS Console. The cluster list may be empty.

🧠 Why It Matters: The EKS Console provides visibility into clusters, node groups, networking, access configuration, and workloads.

## 🔹 Step 2 — Understand EKS Cluster Components

🎯 Objective: Identify the components of an EKS cluster.

| Component          | Responsibility                        |
| ------------------ | ------------------------------------- |
| EKS Control Plane  | Runs Kubernetes management components |
| API Server         | Receives Kubernetes API requests      |
| Scheduler          | Assigns Pods to suitable nodes        |
| Controller Manager | Maintains desired Kubernetes state    |
| etcd               | Stores Kubernetes cluster state       |
| Worker Nodes       | Run application containers            |
| kubelet            | Manages Pods on worker nodes          |
| Container Runtime  | Runs containers on worker nodes       |

### EKS Control Plane vs Worker Nodes

| Feature                               | Control Plane      | Worker Nodes                       |
| ------------------------------------- | ------------------ | ---------------------------------- |
| Managed by                            | AWS                | AWS-managed node group or customer |
| Runs applications                     | No                 | Yes                                |
| Runs Kubernetes management components | Yes                | No                                 |
| Uses EC2 instances in your account    | No                 | Yes, for EC2 node groups           |
| Main purpose                          | Cluster management | Application execution              |

✅ Expected: You can explain the difference between the Kubernetes control plane and worker nodes.

## 🔹 Step 3 — Explore EKS Compute Options

🎯 Objective: Understand how Kubernetes workloads run on AWS.

Amazon EKS supports several compute approaches.

| Compute Option      | Description                                                             |
| ------------------- | ----------------------------------------------------------------------- |
| Managed Node Groups | EC2 worker nodes with AWS-managed lifecycle operations                  |
| Self-Managed Nodes  | EC2 worker nodes managed by the customer                                |
| AWS Fargate         | Serverless compute for supported Kubernetes Pods                        |
| EKS Auto Mode       | AWS automates more of the cluster compute and infrastructure management |

For this training, we will use EKS managed node groups.

Why?

- Students can inspect EC2 worker nodes.
- It is easier to understand Pods and node relationships.
- It supports practical Kubernetes troubleshooting.
- It provides hands-on exposure to Kubernetes infrastructure.

✅ Expected: You understand why managed EC2 node groups are selected for the course.

## 🔹 Step 4 — Explore Amazon VPC Networking

🎯 Objective: Identify the networking components used by EKS.

1. Open AWS Console → VPC.
2. Select Your VPCs.
3. Review the available VPCs.
4. Select Subnets.
5. Review subnet Availability Zones.
6. Open Security Groups.

### EKS Networking Components

| Component         | Purpose                                                          |
| ----------------- | ---------------------------------------------------------------- |
| VPC               | Isolated AWS networking environment                              |
| Subnet            | IP address range within a VPC                                    |
| Availability Zone | AWS infrastructure location                                      |
| Security Group    | Controls allowed network traffic                                 |
| Route Table       | Determines traffic routing                                       |
| Internet Gateway  | Enables internet connectivity for appropriately routed resources |
| NAT Gateway       | Enables outbound internet access from private subnets            |

Important: The EKS cluster creation script in Lab 02003 will provision the required networking resources using `eksctl`.

⚠️ Cost Note: NAT Gateways, EC2 instances, load balancers, and EKS clusters may generate AWS charges.

✅ Expected: You can locate VPCs, subnets, and security groups in the AWS Console.

## 🔹 Step 5 — Understand EKS IAM Requirements

🎯 Objective: Learn how AWS IAM controls EKS infrastructure access.

1. Open AWS Console → IAM.
2. Select Roles.
3. Explore existing IAM roles.
4. Select Policies.
5. Search for the following AWS-managed policies.

| IAM Policy                           | Purpose                                              |
| ------------------------------------ | ---------------------------------------------------- |
| `AmazonEKSClusterPolicy`             | Permissions used by the EKS cluster IAM role         |
| `AmazonEKSWorkerNodePolicy`          | Permissions required by EKS worker nodes             |
| `AmazonEC2ContainerRegistryPullOnly` | Permissions for pulling images from ECR              |
| `AmazonEKS_CNI_Policy`               | Permissions used by the Amazon VPC CNI when required |

Important: These policies serve different AWS identities and roles. Do not attach all of them to your personal IAM identity.

For modern configurations, the VPC CNI can use a dedicated IAM role or EKS Pod Identity rather than relying on broad node-role permissions.

✅ Expected: You understand that cluster administration, node operations, and workload permissions are separate responsibilities.

## 🔹 Step 6 — Understand EKS Access Management

🎯 Objective: Understand how users access Kubernetes clusters.

Amazon EKS supports EKS access entries for managing Kubernetes API access.

### Access Flow

```
IAM User / IAM Role
        │
        ▼
AWS Authentication
        │
        ▼
EKS Access Entry
        │
        ▼
EKS Access Policy
or Kubernetes RBAC
        │
        ▼
Kubernetes API
```

Key Concepts:

- IAM: Identifies and authenticates AWS principals.
- EKS access entries: Associate IAM principals with Kubernetes cluster access.
- EKS access policies: Provide AWS-managed Kubernetes authorization options.
- Kubernetes RBAC: Controls Kubernetes permissions through Roles and RoleBindings.

Note: EKS access policies and Kubernetes RBAC are distinct authorization mechanisms. Depending on the cluster configuration, either or both may be used.

✅ Expected: You understand how AWS identities can receive Kubernetes API access.

## 🔹 Step 7 — Review EKS Cluster Creation Prerequisites

🎯 Objective: Identify everything required before provisioning a cluster.

| Requirement            | Purpose                              | Status            |
| ---------------------- | ------------------------------------ | ----------------- |
| AWS Account            | Hosts EKS resources                  | Required          |
| AWS Region             | Cluster deployment location          | Required          |
| IAM Permissions        | Authorizes cluster creation          | Required          |
| AWS CLI v2             | AWS authentication and configuration | Next Lab          |
| kubectl                | Kubernetes administration            | Next Lab          |
| eksctl                 | EKS cluster creation and deletion    | Next Lab          |
| VPC & Subnets          | Cluster networking                   | Created by script |
| EC2 Managed Node Group | Kubernetes worker nodes              | Created by script |

### Planned Training Resources

| Resource           | Planned Value                          |
| ------------------ | -------------------------------------- |
| Cluster name       | `vyomanant-eks`                        |
| Region             | `ap-south-1` (example)                 |
| Compute            | Managed EC2 Node Group                 |
| Node count         | 2                                      |
| Node instance type | `t3.medium`                            |
| Kubernetes version | Supported EKS default at creation time |

Note: These are planned values, not resources created in this lab. The creation script will validate the selected Region, available instance types, and compatible Kubernetes version.

⚠️ Cost Warning: A running EKS cluster incurs charges for the control plane and associated infrastructure. Keep the cluster only as long as necessary for the training.

## 🔹 Step 8 — Review the Cluster Lifecycle Workflow

🎯 Objective: Understand how we will manage EKS infrastructure in upcoming labs.

```
Lab 02002
Install AWS CLI, kubectl, eksctl
                │
                ▼
Lab 02003
Run Cluster Creation Script
                │
                ▼
AWS EKS Cluster
Managed Node Group
                │
                ▼
Deploy Kubernetes Applications
                │
                ▼
ECR + Docker Hub Integration
GitHub Actions CI/CD
                │
                ▼
Run Cluster Deletion Script
                │
                ▼
Verify AWS Resource Cleanup
```

Important: We will use reusable scripts for cluster creation and deletion, while using the AWS Console for infrastructure inspection and `kubectl` for Kubernetes operations.

✅ Expected: You understand the complete cluster lifecycle planned for the training.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🔎 Verification

| Verification                     | Expected Result |
| -------------------------------- | --------------- |
| AWS Console access               | Successful      |
| EKS Console accessible           | Yes             |
| EKS architecture understood      | Yes             |
| Managed node groups understood   | Yes             |
| VPC networking explored          | Completed       |
| IAM policies reviewed            | Completed       |
| EKS access management understood | Yes             |
| Cluster prerequisites identified | Yes             |
| AWS resources created            | None            |

Lab completion criteria: You understand the EKS architecture and have identified the AWS prerequisites required before cluster creation.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🛠️ Troubleshooting

| Issue                           | Possible Cause                           | Resolution                                |
| ------------------------------- | ---------------------------------------- | ----------------------------------------- |
| EKS Console access denied       | Missing IAM permissions                  | Request the required EKS read permissions |
| VPC resources not visible       | Wrong Region or insufficient permissions | Verify AWS Region and IAM access          |
| IAM policies not visible        | Missing IAM read permissions             | Contact your AWS administrator            |
| Cluster list is empty           | No EKS cluster exists                    | Expected before Lab 02003                 |
| Unable to inspect EC2 resources | Missing EC2 read permissions             | Request appropriate read-only access      |

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧹 Cleanup

No AWS resources were created in this lab.

No cleanup is required.

Important: Do not delete existing VPCs, IAM roles, security groups, or other AWS resources while exploring the Console.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧠 Key Takeaways

1. Amazon EKS is a managed Kubernetes service.
2. AWS manages the EKS Kubernetes control plane.
3. Managed node groups provide EC2 worker nodes for applications.
4. Amazon VPC provides networking for EKS workloads.
5. IAM controls AWS infrastructure access.
6. EKS access entries and Kubernetes RBAC control Kubernetes API access.
7. AWS CLI, kubectl, and eksctl support EKS administration.
8. EKS infrastructure must be cleaned up to avoid unnecessary charges.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 📚 Official Documentation

- [Amazon EKS Documentation](https://docs.aws.amazon.com/eks/latest/userguide/what-is-eks.html?utm_source=chatgpt.com)
- [Amazon EKS Clusters](https://docs.aws.amazon.com/eks/latest/userguide/clusters.html?utm_source=chatgpt.com)
- [Amazon EKS Managed Node Groups](https://docs.aws.amazon.com/eks/latest/userguide/managed-node-groups.html?utm_source=chatgpt.com)
- [Amazon EKS Networking](https://docs.aws.amazon.com/eks/latest/userguide/eks-networking.html?utm_source=chatgpt.com)
- [Amazon EKS IAM Roles](https://docs.aws.amazon.com/eks/latest/userguide/service_IAM_role.html?utm_source=chatgpt.com)
- [Amazon EKS Access Entries](https://docs.aws.amazon.com/eks/latest/userguide/access-entries.html?utm_source=chatgpt.com)
- [Amazon EKS Pricing](https://aws.amazon.com/eks/pricing/?utm_source=chatgpt.com)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Next Lab: 02002 — Install and Configure AWS CLI, kubectl & eksctl

In the next lab, we will install and verify the essential tools required to create and manage EKS clusters using reusable shell scripts.
