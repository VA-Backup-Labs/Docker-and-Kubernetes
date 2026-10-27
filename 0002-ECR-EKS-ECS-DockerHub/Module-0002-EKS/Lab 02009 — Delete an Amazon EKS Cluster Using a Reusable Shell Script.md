# 🧪 Lab 02009 — Delete an Amazon EKS Cluster Using a Reusable Shell Script

Create a reusable shell script to safely delete an Amazon EKS cluster, understand the cleanup sequence, and verify the removal of AWS infrastructure.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🌍 Lab Overview

In Lab 02003, we created an EKS cluster using a reusable shell script and `eksctl`.

In this lab, we will create the corresponding EKS cluster deletion script.

The script will help remove the cluster, managed node groups, and infrastructure managed by `eksctl`.

Important: We will prepare the deletion script now but execute it only after completing Module 03 — ECR + Docker Hub + EKS + GitHub Actions Integration.

| Property         | Details                               |
| ---------------- | ------------------------------------- |
| Lab ID           | 02009                                 |
| Module           | 02 — Amazon EKS                       |
| Difficulty       | Beginner                              |
| Duration         | 20–25 minutes                         |
| Tools            | AWS CLI, eksctl, kubectl, AWS Console |
| Environment      | Ubuntu Linux + AWS                    |
| Cluster          | `vyomanant-eks`                       |
| Region           | `ap-south-1`                          |
| Deletion Method  | Reusable Bash Script                  |
| Execution Status | Prepare Now, Execute After Module 03  |

⚠️ Cost Warning: The EKS cluster and associated AWS infrastructure continue generating charges until they are deleted.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🎯 Learning Objectives

By the end of this lab, you will be able to:

1. Understand the EKS cluster deletion process.
2. Identify resources managed by `eksctl`.
3. Create a reusable cluster deletion script.
4. Add confirmation before destructive operations.
5. Understand why Kubernetes-created load balancers require cleanup.
6. Verify cluster deletion using AWS CLI and Console.
7. Identify resources that may remain after deletion.
8. Plan safe cleanup after completing the training.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧰 Prerequisites

Ensure you have:

- Completed Labs 02001–02008.
- An existing EKS cluster named `vyomanant-eks`.
- AWS CLI, kubectl, and eksctl installed.
- An AWS CLI profile named `vyomanant`.
- IAM permissions to delete EKS, EC2, IAM, and CloudFormation resources.
- Access to the AWS Management Console.
- The original `eks-lab` directory from Lab 02003.

### 🔎 Verify AWS Authentication

```
export AWS_PROFILE=vyomanant
```

```
aws sts get-caller-identity
```

✅ Expected: Your AWS account and authenticated identity are displayed.

⚠️ Important: Confirm that you are using the correct AWS account before preparing any deletion operations.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🏗️ Lab Architecture

```
                  Ubuntu Linux
┌─────────────────────────────────────────────┐
│                                             │
│  eks-cluster-delete.sh                      │
│              │                              │
│              ▼                              │
│  Confirmation: DELETE                      │
│              │                              │
│              ▼                              │
│  eksctl delete cluster                      │
│                                             │
└──────────────────────┬──────────────────────┘
                       │
                       ▼
                   AWS Cloud
┌─────────────────────────────────────────────┐
│                                             │
│  Amazon EKS: vyomanant-eks                  │
│              │                              │
│              ▼                              │
│  AWS CloudFormation                         │
│              │                              │
│       ┌──────┴──────┐                       │
│       ▼             ▼                       │
│  EKS Cluster    Managed Node Group          │
│                 workers                     │
│       │             │                       │
│       ▼             ▼                       │
│  Control Plane   EC2 Worker Nodes           │
│                                             │
│  eksctl-managed VPC / Subnets               │
│  Security Groups / IAM Roles                │
│                                             │
└─────────────────────────────────────────────┘
```

Architecture explanation:

- The deletion script invokes `eksctl`.
- `eksctl` removes the cluster and infrastructure it manages, primarily through AWS CloudFormation.
- Kubernetes-created external resources should be removed before cluster deletion.
- Independently created AWS resources are not automatically deleted.

Note: This diagram represents the future cleanup process. The cluster remains running during this lab.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🚀 Step-by-Step Instructions

## 🔹 Step 1 — Verify the Existing EKS Cluster

🎯 Objective: Confirm the cluster that will eventually be deleted.

```
eksctl get cluster --region ap-south-1
```

✅ Expected (illustrative):

```
NAME             REGION       EKSCTL CREATED
vyomanant-eks    ap-south-1   True
```

Verify the worker nodes:

```
kubectl get nodes
```

Expected: Two worker nodes are in the `Ready` state.

Important: Do not delete the cluster yet.

## 🔹 Step 2 — Understand EKS Resource Cleanup

🎯 Objective: Identify resources involved in cluster deletion.

| Resource                            | Cleanup Method                   |
| ----------------------------------- | -------------------------------- |
| EKS control plane                   | eksctl                           |
| Managed node group                  | eksctl / CloudFormation          |
| EC2 worker nodes                    | Managed node group deletion      |
| eksctl-created VPC                  | CloudFormation                   |
| eksctl-created subnets              | CloudFormation                   |
| eksctl-created security groups      | CloudFormation                   |
| eksctl-created IAM roles            | CloudFormation                   |
| Kubernetes LoadBalancer Services    | Delete Kubernetes Services first |
| ECR repositories                    | Separate cleanup                 |
| Independently created AWS resources | Separate cleanup                 |

⚠️ Warning: AWS resources created outside the eksctl-managed infrastructure may remain and continue generating charges.

## 🔹 Step 3 — Review Kubernetes Application Resources

🎯 Objective: Identify Kubernetes workloads before cluster deletion.

```
kubectl get all -n vyomanant-lab
```

Expected: The NGINX Deployment, Pods, and ClusterIP Service are displayed.

Check Services across namespaces:

```
kubectl get services -A
```

🔎 Verify: Identify any Services with type `LoadBalancer`.

Important: Delete Kubernetes LoadBalancer Services before deleting the cluster so their AWS load balancers can be cleaned up correctly.

Do not delete the application resources now. They will be reused in Module 03.

## 🔹 Step 4 — Create the EKS Deletion Script

🎯 Objective: Prepare a reusable script with a safety confirmation.

Navigate to the original directory:

```
cd eks-lab
```

Create the script:

```
nano eks-cluster-delete.sh
```

Add the following:

```
#!/bin/bash

set -e

export AWS_PROFILE=vyomanant

CLUSTER_NAME="vyomanant-eks"
AWS_REGION="ap-south-1"

echo "======================================"
echo " Amazon EKS Cluster Deletion"
echo "======================================"
echo "Cluster: $CLUSTER_NAME"
echo "Region : $AWS_REGION"
echo ""

echo "WARNING: This operation deletes the EKS cluster"
echo "and its eksctl-managed infrastructure."
echo ""

read -p "Type DELETE to confirm: " CONFIRM

if [ "$CONFIRM" != "DELETE" ]; then
    echo "Deletion cancelled."
    exit 0
fi

echo "Deleting EKS cluster..."

eksctl delete cluster \
  --name "$CLUSTER_NAME" \
  --region "$AWS_REGION" \
  --wait

echo "EKS cluster deletion completed."
```

### Script Explanation

| Component               | Purpose                                   |
| ----------------------- | ----------------------------------------- |
| `set -e`                | Stops execution if a command fails        |
| `AWS_PROFILE`           | Selects the AWS authentication profile    |
| `CLUSTER_NAME`          | Specifies the cluster                     |
| `AWS_REGION`            | Specifies the Region                      |
| `read -p`               | Requests confirmation                     |
| `DELETE`                | Required confirmation text                |
| `eksctl delete cluster` | Deletes the cluster                       |
| `--wait`                | Waits for deletion operations to complete |

⚠️ Warning: This script is destructive when the confirmation is accepted. Never run it against a production cluster without an approved change procedure.

## 🔹 Step 5 — Make the Script Executable

🎯 Objective: Prepare the script for future execution.

```
chmod +x eks-cluster-delete.sh
```

Verify the script exists:

```
ls -l eks-cluster-delete.sh
```

✅ Expected: The file has executable permissions.

## 🔹 Step 6 — Validate the Script Without Deleting the Cluster

🎯 Objective: Check script syntax safely.

```
bash -n eks-cluster-delete.sh
```

✅ Expected: No output and no syntax errors.

Important: `bash -n` checks shell syntax without executing the script.

### Optional Confirmation Test

Run:

```
./eks-cluster-delete.sh
```

When prompted:

```
Type DELETE to confirm:
```

Enter:

```
CANCEL
```

✅ Expected:

```
Deletion cancelled.
```

⚠️ Critical: Do not enter `DELETE` during this lab.

## 🔹 Step 7 — Understand the Final Deletion Procedure

🎯 Objective: Learn the steps to perform after Module 03.

After completing all integration labs:

1. Delete Kubernetes Services of type `LoadBalancer`.
2. Verify their associated AWS load balancers are removed.
3. Remove any other Kubernetes-created external AWS resources.
4. Confirm the correct AWS account and Region.
5. Run the cluster deletion script.
6. Enter `DELETE` when prompted.
7. Wait for the deletion process to complete.

### Final Deletion Command

Execute only after Module 03:

```
./eks-cluster-delete.sh
```

When prompted, enter:

```
DELETE
```

Expected: eksctl starts deleting the cluster and associated infrastructure.

Note: Cluster deletion can take several minutes. Some infrastructure may take additional time to disappear.

## 🔹 Step 8 — Understand Post-Deletion Verification

🎯 Objective: Learn how to confirm successful cleanup.

After executing the deletion script, verify the cluster:

```
aws eks describe-cluster --name vyomanant-eks --region ap-south-1
```

Expected after successful deletion: AWS returns a `ResourceNotFoundException`.

Verify the cluster list:

```
eksctl get cluster --region ap-south-1
```

Expected: `vyomanant-eks` is not listed.

### AWS Console Verification

After final deletion, inspect:

| AWS Console Section     | Expected                         |
| ----------------------- | -------------------------------- |
| EKS → Clusters          | `vyomanant-eks` absent           |
| EC2 → Instances         | Training worker nodes terminated |
| EC2 → Load Balancers    | Training load balancers absent   |
| CloudFormation → Stacks | Training stacks deleted          |
| VPC → Your VPCs         | eksctl-created VPC removed       |
| EC2 → Security Groups   | eksctl-created groups removed    |

Important: Verify only the resources associated with this training cluster. Do not delete unrelated AWS resources.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🔎 Verification

| Check                                       | Expected Result |
| ------------------------------------------- | --------------- |
| Existing cluster identified                 | Yes             |
| Kubernetes resources reviewed               | Yes             |
| LoadBalancer cleanup requirement understood | Yes             |
| Deletion script created                     | Yes             |
| Script executable                           | Yes             |
| Bash syntax validation                      | Passed          |
| Confirmation cancellation tested            | Passed          |
| EKS cluster deleted now                     | No              |
| Cluster retained for Module 03              | Yes             |

Lab completion criteria: You have a reusable, syntax-validated EKS deletion script ready for execution after Module 03.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🛠️ Troubleshooting

| Issue                          | Possible Cause                             | Resolution                                                |
| ------------------------------ | ------------------------------------------ | --------------------------------------------------------- |
| `eksctl: command not found`    | eksctl not installed or PATH issue         | Verify installation from Lab 02002                        |
| `AccessDenied` during deletion | Missing IAM permissions                    | Review required EKS and CloudFormation permissions        |
| CloudFormation deletion fails  | Dependent AWS resources remain             | Inspect stack events and remove blocking resources safely |
| VPC deletion fails             | Load balancer or network interfaces remain | Identify and clean up cluster-created dependencies        |
| Cluster still visible          | Deletion incomplete                        | Check eksctl output and CloudFormation status             |

### 🔎 Inspect CloudFormation Stacks

```
aws cloudformation list-stacks --region ap-south-1
```

Note: This command lists stack summaries, including stacks unrelated to the lab. Identify only stacks associated with `vyomanant-eks`.

⚠️ Warning: Never delete unrelated CloudFormation stacks to resolve a training cleanup issue.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧹 Cleanup

No EKS resources should be deleted during this lab.

Keep the following resources for Module 03:

| Resource                     | Action |
| ---------------------------- | ------ |
| EKS cluster `vyomanant-eks`  | Keep   |
| Managed node group `workers` | Keep   |
| Namespace `vyomanant-lab`    | Keep   |
| NGINX Deployment             | Keep   |
| NGINX ClusterIP Service      | Keep   |
| ECR repository `myapp`       | Keep   |
| `eks-cluster-create.sh`      | Keep   |
| `eks-cluster-delete.sh`      | Keep   |

⚠️ Cost Reminder: Keeping the EKS cluster running incurs AWS charges. If you are not continuing the course, perform the documented final deletion procedure rather than leaving unused infrastructure running.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧠 Key Takeaways

1. EKS clusters require planned infrastructure cleanup.
2. eksctl can delete clusters and their managed infrastructure.
3. Reusable shell scripts simplify repeated cleanup operations.
4. Destructive scripts should include confirmation prompts.
5. Kubernetes-created load balancers should be removed before cluster deletion.
6. CloudFormation manages much of the infrastructure created by eksctl.
7. AWS Console verification helps identify leftover resources.
8. EKS cluster deletion should be completed after all dependent training labs.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 📚 Official Documentation

- [Amazon EKS Cluster Deletion](https://docs.aws.amazon.com/eks/latest/userguide/delete-cluster.html?utm_source=chatgpt.com)
- [eksctl Cluster Management](https://eksctl.io/usage/creating-and-managing-clusters/?utm_source=chatgpt.com)
- [eksctl Delete Cluster](https://eksctl.io/usage/creating-and-managing-clusters/?utm_source=chatgpt.com#deleting-a-cluster)
- [AWS CloudFormation Stacks](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/stacks.html?utm_source=chatgpt.com)
- [Kubernetes Services](https://kubernetes.io/docs/concepts/services-networking/service/?utm_source=chatgpt.com)
- [Amazon EKS Pricing](https://aws.amazon.com/eks/pricing/?utm_source=chatgpt.com)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

✅ Module 02 — Amazon EKS Completed (9/9 Labs)

Next: Module 03 — ECR + Docker Hub + EKS + GitHub Actions Integration

Lab 03001 — Understand the Complete Container CI/CD Integration Architecture

We will connect the concepts of Docker image registries, Amazon ECR, Docker Hub, Amazon EKS, and GitHub Actions before implementing an end-to-end CI/CD pipeline.
