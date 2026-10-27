# 🧪 Lab 02002 — Install and Configure AWS CLI, kubectl & eksctl

Install and verify the essential command-line tools required to create, access, and manage Amazon EKS clusters using reusable shell scripts.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🌍 Lab Overview

Before creating an Amazon EKS cluster, we need three essential tools:

- AWS CLI: Authenticates with AWS and manages AWS service access.
- kubectl: Manages Kubernetes resources such as Pods, Deployments, and Services.
- eksctl: Creates and manages Amazon EKS clusters.

In this lab, we will install these tools on an Ubuntu Linux machine, configure AWS authentication, and verify the environment.

| Property              | Details                         |
| --------------------- | ------------------------------- |
| Lab ID                | 02002                           |
| Module                | 02 — Amazon EKS                 |
| Difficulty            | Beginner                        |
| Duration              | 20–30 minutes                   |
| Tools                 | AWS CLI v2, kubectl, eksctl     |
| Environment           | Ubuntu 22.04/24.04 LTS (x86_64) |
| Approach              | CLI for required setup          |
| AWS Resources Created | None                            |

Note: This lab uses Ubuntu Linux. Windows and macOS installation methods are available in the official documentation.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🎯 Learning Objectives

By the end of this lab, you will be able to:

1. Identify the purpose of AWS CLI, kubectl, and eksctl.
2. Install AWS CLI v2 on Ubuntu.
3. Configure AWS authentication.
4. Verify the active AWS identity.
5. Install kubectl.
6. Install eksctl.
7. Configure a default AWS Region.
8. Validate the environment before EKS cluster creation.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧰 Prerequisites

Ensure you have:

- An Ubuntu 22.04 or 24.04 LTS machine.
- Internet connectivity.
- A terminal with `sudo` permissions.
- An active AWS account.
- An authorized IAM identity with permissions for EKS cluster creation.
- Completed Lab 02001 — Amazon EKS Architecture, IAM, VPC & Cluster Prerequisites.

⚠️ Security: Use IAM Identity Center or another approved temporary-credential method where possible. Avoid using root account credentials or storing long-lived AWS access keys unnecessarily.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🏗️ Lab Architecture

```
┌──────────────────────────────────┐
│        Ubuntu Linux Machine      │
│                                  │
│  ┌────────────────────────────┐  │
│  │ AWS CLI v2                 │  │
│  │ AWS Authentication        │  │
│  └──────────────┬─────────────┘  │
│                 │                │
│  ┌──────────────▼─────────────┐  │
│  │ eksctl                     │  │
│  │ EKS Cluster Management     │  │
│  └────────────────────────────┘  │
│                                  │
│  ┌────────────────────────────┐  │
│  │ kubectl                    │  │
│  │ Kubernetes Management      │  │
│  └────────────────────────────┘  │
└────────────────┬─────────────────┘
                 │
                 │ AWS API Access
                 ▼
┌──────────────────────────────────┐
│            AWS Cloud             │
│                                  │
│  IAM / AWS STS                   │
│  Amazon EKS (Next Lab)           │
└──────────────────────────────────┘
```

Architecture explanation: AWS CLI provides AWS authentication, `eksctl` manages EKS infrastructure, and `kubectl` communicates with the Kubernetes API after cluster access is configured.

No EKS cluster is created in this lab.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🚀 Step-by-Step Instructions

## 🔹 Step 1 — Update Ubuntu and Install Dependencies

🎯 Objective: Prepare the Ubuntu machine for tool installation.

```
sudo apt update
```

```
sudo apt install -y curl unzip tar
```

✅ Expected: Required packages are installed successfully.

## 🔹 Step 2 — Install AWS CLI v2

🎯 Objective: Install the AWS Command Line Interface.

Download the AWS CLI installer for Linux x86_64.

```
curl -o awscliv2.zip https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip
```

Extract the installer.

```
unzip awscliv2.zip
```

Install AWS CLI.

```
sudo ./aws/install
```

Verify installation.

```
aws --version
```

✅ Expected (illustrative):

```
aws-cli/2.x.x Python/3.x Linux/x86_64
```

Note: For an existing AWS CLI v2 installation, follow the official update instructions rather than attempting a second installation.

## 🔹 Step 3 — Configure AWS Authentication

🎯 Objective: Authenticate the AWS CLI using IAM Identity Center.

AWS Console preparation:

1. Sign in to your organization's AWS access portal.
2. Obtain the IAM Identity Center start URL and Region from your administrator.
3. Confirm that your assigned AWS account and permission set provide the required lab access.

Configure AWS CLI SSO.

```
aws configure sso
```

Follow the prompts to provide the start URL, SSO Region, account, role, and CLI profile name.

For consistency, use the profile name:

```
vyomanant
```

Sign in.

```
aws sso login --profile vyomanant
```

Verify your AWS identity.

```
aws sts get-caller-identity --profile vyomanant
```

✅ Expected (illustrative):

```
{
    "UserId": "EXAMPLEUSERID",
    "Account": "123456789012",
    "Arn": "arn:aws:sts::123456789012:assumed-role/ExampleRole/ExampleUser"
}
```

Important: The account ID and role shown above are examples.

Note: If your AWS environment uses a different approved authentication method, configure that method instead. The remaining labs will assume a working `vyomanant` AWS CLI profile.

## 🔹 Step 4 — Configure AWS Region and Profile

🎯 Objective: Set the default AWS Region and active profile for the training.

Set the Region for the profile.

```
aws configure set region ap-south-1 --profile vyomanant
```

Note: Replace `ap-south-1` if your training uses a different AWS Region.

Set the active profile for the current terminal session.

```
export AWS_PROFILE=vyomanant
```

Verify the configuration.

```
aws configure list
```

✅ Expected: The AWS CLI uses the `vyomanant` profile and the selected Region.

Important: `export` applies only to the current terminal session. Run it again when opening a new terminal, or explicitly select the profile when required.

## 🔹 Step 5 — Install kubectl

🎯 Objective: Install the Kubernetes command-line client.

Download the latest stable Linux AMD64 binary.

```
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
```

Install the binary.

```
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
```

Verify installation.

```
kubectl version --client
```

✅ Expected (illustrative):

```
Client Version: v1.x.x
```

Important: For EKS, the kubectl client should be within one minor version of the Kubernetes control plane version. Before creating the cluster, confirm that the installed version is compatible with the selected EKS version.

## 🔹 Step 6 — Install eksctl

🎯 Objective: Install the official EKS cluster management CLI.

Download the latest eksctl release for Linux AMD64.

```
curl -sLO "https://github.com/eksctl-io/eksctl/releases/latest/download/eksctl_Linux_amd64.tar.gz"
```

Extract the archive.

```
tar -xzf eksctl_Linux_amd64.tar.gz
```

Install the binary.

```
sudo mv eksctl /usr/local/bin/
```

Verify installation.

```
eksctl version
```

✅ Expected (illustrative):

```
0.x.x
```

Note: The actual version depends on the latest available release.

Security recommendation: In production environments, verify downloaded binaries using official checksums or signatures before installation.

## 🔹 Step 7 — Verify the Complete Toolchain

🎯 Objective: Confirm that all three tools are installed and available.

```
aws --version
```

```
kubectl version --client
```

```
eksctl version
```

Verify AWS access.

```
aws sts get-caller-identity
```

✅ Expected: All tools return version information, and AWS STS returns the authenticated AWS identity.

🧠 Why It Matters: These tools will be used by the EKS cluster creation and deletion scripts in upcoming labs.

## 🔹 Step 8 — Understand the EKS Cluster Management Workflow

🎯 Objective: Prepare for the next lab.

The upcoming EKS labs will use:

```
eks-cluster-create.sh
eks-cluster-delete.sh
```

### Planned Cluster Configuration

| Setting         | Value                |
| --------------- | -------------------- |
| Cluster name    | `vyomanant-eks`      |
| Region          | `ap-south-1`         |
| Node group      | Managed EC2 nodes    |
| Desired nodes   | 2                    |
| Instance type   | `t3.medium`          |
| Creation method | Bash script + eksctl |
| Deletion method | Bash script + eksctl |

Important: These are planned settings. Cluster creation will occur in Lab 02003.

⚠️ AWS Cost Warning: The next lab will provision billable AWS infrastructure, including an EKS control plane and EC2 worker nodes. Depending on the networking configuration, additional charges may apply.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🔎 Verification

| Check               | Expected Result |
| ------------------- | --------------- |
| Ubuntu dependencies | Installed       |
| AWS CLI v2          | Installed       |
| AWS authentication  | Successful      |
| AWS Region          | Configured      |
| kubectl             | Installed       |
| eksctl              | Installed       |
| AWS identity        | Verified        |
| EKS cluster created | No              |

Lab completion criteria: AWS CLI, kubectl, and eksctl are installed and ready for EKS cluster creation.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🛠️ Troubleshooting

| Issue                          | Possible Cause                              | Resolution                                   |
| ------------------------------ | ------------------------------------------- | -------------------------------------------- |
| `aws: command not found`       | AWS CLI installation or PATH issue          | Recheck installation and open a new terminal |
| AWS SSO login fails            | Incorrect SSO URL, Region, or assignment    | Verify details with your AWS administrator   |
| `Unable to locate credentials` | Profile not selected or session unavailable | Set `AWS_PROFILE` and authenticate again     |
| `kubectl: command not found`   | Binary not installed correctly              | Verify `/usr/local/bin/kubectl`              |
| `eksctl: command not found`    | Binary not in PATH                          | Verify `/usr/local/bin/eksctl`               |

### 🔎 Check AWS Profile

```
aws configure list-profiles
```

### 🔎 Reauthenticate When the SSO Session Expires

```
aws sso login --profile vyomanant
```

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧹 Cleanup

No AWS infrastructure was created in this lab.

You may remove the downloaded installation files from the current directory.

```
rm -f awscliv2.zip
```

```
rm -rf aws
```

```
rm -f kubectl eksctl_Linux_amd64.tar.gz
```

⚠️ Warning: Run these commands only from the directory used for this lab. The `aws` directory removal is intended solely for the extracted AWS CLI installer.

Keep the installed AWS CLI, kubectl, and eksctl binaries for subsequent labs.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧠 Key Takeaways

1. AWS CLI enables authenticated AWS service access.
2. IAM Identity Center supports temporary AWS CLI credentials.
3. AWS profiles help organize account and Region configurations.
4. kubectl manages Kubernetes resources.
5. eksctl simplifies Amazon EKS cluster management.
6. kubectl version compatibility matters when accessing EKS clusters.
7. EKS cluster creation can be automated using reusable Bash scripts.
8. AWS infrastructure should be provisioned and cleaned up carefully to manage costs.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 📚 Official Documentation

- [Install AWS CLI v2](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html?utm_source=chatgpt.com)
- [Configure AWS CLI with IAM Identity Center](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-sso.html?utm_source=chatgpt.com)
- [Install kubectl on Linux](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/?utm_source=chatgpt.com)
- [Install eksctl](https://eksctl.io/installation/?utm_source=chatgpt.com)
- [Create an EKS Cluster with eksctl](https://docs.aws.amazon.com/eks/latest/userguide/getting-started-eksctl.html?utm_source=chatgpt.com)
- [Amazon EKS Kubernetes Versions](https://docs.aws.amazon.com/eks/latest/userguide/kubernetes-versions.html?utm_source=chatgpt.com)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Next Lab: 02003 — Create an Amazon EKS Cluster Using a Reusable Shell Script

We will create the `vyomanant-eks` cluster with managed EC2 worker nodes, verify it using kubectl and the AWS Console, and prepare it for application deployment.
