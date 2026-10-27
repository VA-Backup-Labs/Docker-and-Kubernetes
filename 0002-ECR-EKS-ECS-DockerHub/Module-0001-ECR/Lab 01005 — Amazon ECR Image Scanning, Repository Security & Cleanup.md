# 🧪 Lab 01005 — Amazon ECR Image Scanning, Repository Security & Cleanup

Learn how to inspect container image vulnerabilities, review ECR security settings, and safely clean up your AWS ECR lab resources using the AWS Management Console.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🌍 Lab Overview

Amazon Elastic Container Registry (ECR) provides security features such as image scanning, IAM-based access control, encryption at rest, and repository policies.

In this lab, you will review the security configuration of the existing `myapp` repository, explore image scanning results, and perform optional cleanup.

This is the final lab of Module 01 — Amazon ECR.

| Property    | Details                    |
| ----------- | -------------------------- |
| Lab ID      | 01005                      |
| Module      | 01 — Amazon ECR            |
| Difficulty  | Beginner                   |
| Duration    | 20–25 minutes              |
| Tools       | AWS Management Console     |
| Environment | AWS ECR Private Repository |
| Repository  | `myapp`                    |
| Approach    | Console Only               |

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🎯 Learning Objectives

By the end of this lab, you will be able to:

1. Understand ECR image scanning.
2. Differentiate between basic and enhanced scanning.
3. Review container image vulnerability findings.
4. Inspect repository access policies.
5. Verify repository encryption settings.
6. Understand image tag immutability as a security control.
7. Identify safe image and repository cleanup procedures.
8. Prepare ECR resources for upcoming EKS integration labs.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧰 Prerequisites

Ensure you have:

- Completed Labs 01001–01004.
- An existing private ECR repository named `myapp`.
- At least one Docker image tagged `v1`.
- AWS Console access.
- IAM permissions to view ECR repositories, scanning settings, and repository policies.

Important: This lab uses the AWS Console only. No CLI commands are required.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🏗️ Lab Architecture

```
┌──────────────────────────────────┐
│       AWS Management Console     │
└─────────────────┬────────────────┘
                  │
                  ▼
┌──────────────────────────────────┐
│           Amazon ECR             │
│                                  │
│  Private Repository: myapp       │
│                                  │
│  ┌────────────────────────────┐  │
│  │ Container Image            │  │
│  │ myapp:v1                   │  │
│  └──────────────┬─────────────┘  │
│                 │                │
│       ┌─────────┴─────────┐      │
│       ▼                   ▼      │
│  Image Scanning      IAM / Policy│
│  Vulnerabilities     Access      │
│                                  │
│  Encryption: AES-256             │
│  Image Tags: Immutable           │
│  Lifecycle Policy: Configured    │
└──────────────────────────────────┘
```

Architecture explanation: The existing ECR repository contains a Docker image and uses security controls for image scanning, access management, encryption, and image retention.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🚀 Step-by-Step Instructions

## 🔹 Step 1 — Open the ECR Repository

🎯 Objective: Access the repository created in previous labs.

1. Sign in to the [AWS Management Console](https://console.aws.amazon.com/?utm_source=chatgpt.com).
2. Search for Elastic Container Registry.
3. Navigate to Private registry → Repositories.
4. Select the repository named `myapp`.
5. Open the image list.

🔎 Verify: Locate the image tagged `v1`.

✅ Expected: The `myapp` repository contains the image uploaded during Lab 01003.

## 🔹 Step 2 — Explore ECR Image Scanning

🎯 Objective: Understand how ECR identifies vulnerabilities in container images.

Amazon ECR supports two main scanning approaches.

| Scanning Type     | Description                                                                                                |
| ----------------- | ---------------------------------------------------------------------------------------------------------- |
| Basic scanning    | Detects supported operating system package vulnerabilities                                                 |
| Enhanced scanning | Uses Amazon Inspector for more comprehensive vulnerability assessment and continuous scanning capabilities |

### Console Navigation

1. Open Amazon ECR.
2. Navigate to Private registry → Scanning.
3. Review the current registry scanning configuration.
4. Identify whether Basic scanning or Enhanced scanning is enabled.

⚠️ Cost Warning: Enhanced scanning uses Amazon Inspector and may generate additional AWS charges.

For this lab: Use basic scanning. Do not enable enhanced scanning unless required.

✅ Expected: You can identify the active image scanning configuration.

## 🔹 Step 3 — Configure Basic Image Scanning

🎯 Objective: Enable basic vulnerability scanning for the training repository.

1. Navigate to Amazon ECR → Private registry → Scanning.
2. Select Edit if the configuration needs to be changed.
3. Choose Basic scanning.
4. Configure scanning rules to include the `myapp` repository.
5. Save the configuration.

Important: ECR scanning configuration applies at the registry level. Changing it may affect other repositories in the same AWS account and Region. If your organization already manages these settings, do not overwrite them; review the existing configuration instead.

✅ Expected: Basic scanning is configured for the `myapp` repository, or an existing scanning configuration is confirmed.

## 🔹 Step 4 — Inspect Image Scan Results

🎯 Objective: Review available vulnerability findings.

1. Navigate to ECR → Private registry → Repositories.
2. Open `myapp`.
3. Select the image tagged `v1`.
4. Locate the image scanning or vulnerability findings section.
5. Review the available scan results.

If an on-demand basic scan option is available, start a scan for the image and wait for it to finish.

### Common Vulnerability Severities

| Severity      | Meaning                            |
| ------------- | ---------------------------------- |
| Critical      | Potentially severe security impact |
| High          | Significant security risk          |
| Medium        | Moderate security risk             |
| Low           | Lower security risk                |
| Informational | Security-related information       |

✅ Expected: The image scan status and any available vulnerability findings are displayed.

Note: Results depend on the image, scan configuration, and available vulnerability data. A scan may report no findings.

## 🔹 Step 5 — Review ECR Repository Access Policies

🎯 Objective: Understand how repository-level access is controlled.

1. Open the `myapp` repository.
2. Navigate to Permissions or the repository policy section.
3. Review any existing repository policy.
4. Identify whether additional principals have been granted access.

### ECR Access Control

| Security Mechanism    | Purpose                                       |
| --------------------- | --------------------------------------------- |
| IAM identity policies | Control permissions granted to AWS identities |
| Repository policies   | Control repository-level access               |
| ECR authentication    | Authorizes Docker registry operations         |
| Least privilege       | Limits access to required actions             |

Important: A private ECR repository can be accessed through appropriate IAM permissions without an explicit repository policy. An empty repository policy is normal.

⚠️ Security: Do not add public or unrestricted access to the training repository.

✅ Expected: You understand the repository's existing access configuration.

## 🔹 Step 6 — Verify Encryption and Image Tag Immutability

🎯 Objective: Review important repository protection settings.

1. Open the `myapp` repository details.
2. Locate the Encryption configuration.
3. Verify the encryption type.
4. Locate Image tag mutability.
5. Confirm the configuration from Lab 01004.

### Expected Configuration

| Setting              | Expected Value |
| -------------------- | -------------- |
| Repository           | `myapp`        |
| Visibility           | Private        |
| Encryption           | AES-256        |
| Image tag mutability | Immutable      |
| Image scanning       | Basic scanning |
| Lifecycle policy     | Configured     |

Note: Repository encryption protects image data at rest. Tag immutability prevents existing tags from being overwritten but does not prevent authorized users from deleting images.

✅ Expected: The repository's encryption and tag mutability settings are confirmed.

## 🔹 Step 7 — Review ECR Lifecycle Policy

🎯 Objective: Confirm that image retention is configured.

1. Open the `myapp` repository.
2. Navigate to Lifecycle policy.
3. Review the rule created in Lab 01004.
4. Confirm the configured image retention count.

### Expected Lifecycle Rule

| Setting       | Value  |
| ------------- | ------ |
| Tag prefix    | `v`    |
| Image count   | 3      |
| Action        | Expire |
| Rule priority | 1      |

✅ Expected: The repository has a lifecycle policy that retains the three most recently pushed matching images.

🧠 Why It Matters: Lifecycle policies help reduce unnecessary image storage and maintain manageable repositories.

## 🔹 Step 8 — Review Safe Image and Repository Cleanup

🎯 Objective: Understand how to remove unused ECR resources safely.

### Option A — Delete an Individual Image

1. Open Amazon ECR → Private registry → Repositories.
2. Select `myapp`.
3. Open the image list.
4. Select an image that is no longer needed.
5. Choose Delete.
6. Review and confirm the action.

⚠️ Warning: Do not delete `myapp:v1` during this lab because it may be reused in the EKS integration module.

### Option B — Delete the Repository

1. Open Amazon ECR → Private registry → Repositories.
2. Select `myapp`.
3. Choose Delete.
4. Review the confirmation.
5. Confirm only if the repository is no longer required.

⚠️ Warning: Deleting a repository and its images is destructive. A repository containing images requires an appropriate force-delete operation, which the Console may handle through its confirmation workflow.

For this training: Keep the repository and image. These cleanup procedures are for reference and should only be performed after completing the integration labs.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🔎 Verification

| Verification                 | Expected Result       |
| ---------------------------- | --------------------- |
| ECR repository accessible    | Yes                   |
| Image `v1` available         | Yes                   |
| Image scanning configuration | Reviewed              |
| Vulnerability findings       | Reviewed if available |
| Repository permissions       | Reviewed              |
| Encryption                   | AES-256               |
| Image tag mutability         | Immutable             |
| Lifecycle policy             | Configured            |
| Repository retained          | Yes                   |

Lab completion criteria: You have reviewed the repository's security configuration, image scanning, and cleanup procedures without deleting resources needed for upcoming labs.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🛠️ Troubleshooting

| Issue                                         | Possible Cause                                            | Resolution                                                    |
| --------------------------------------------- | --------------------------------------------------------- | ------------------------------------------------------------- |
| Image scan results unavailable                | Image has not been scanned                                | Check scanning configuration and initiate a scan if supported |
| Access denied while viewing scanning settings | Missing IAM permissions                                   | Request the required ECR permissions                          |
| Repository policy is empty                    | No explicit repository policy configured                  | This is normal when IAM policies provide access               |
| Scanning configuration cannot be modified     | Organization-managed settings or insufficient permissions | Contact the AWS administrator                                 |
| Image deletion fails                          | Missing permissions or repository restrictions            | Review IAM permissions and repository configuration           |

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧹 Cleanup

No cleanup is required at this stage.

Retain the following resources:

| Resource                | Action |
| ----------------------- | ------ |
| ECR repository `myapp`  | Keep   |
| Docker image `myapp:v1` | Keep   |
| Image tag immutability  | Keep   |
| Lifecycle policy        | Keep   |

These resources will be reused in Module 03 — ECR + Docker Hub + EKS + GitHub Actions Integration.

Cost note: ECR image storage may incur AWS charges. Delete training resources after the full course when they are no longer needed.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧠 Key Takeaways

1. Amazon ECR provides image scanning capabilities.
2. Basic scanning helps identify supported container image vulnerabilities.
3. Enhanced scanning integrates with Amazon Inspector and may incur additional charges.
4. IAM policies and repository policies control access to ECR.
5. Private repositories do not require explicit repository policies for every use case.
6. Encryption protects container image data at rest.
7. Image tag immutability prevents accidental overwriting of existing tags.
8. Lifecycle policies help manage image retention and storage costs.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 📚 Official Documentation

- [Amazon ECR Image Scanning](https://docs.aws.amazon.com/AmazonECR/latest/userguide/image-scanning.html?utm_source=chatgpt.com)
- [Amazon ECR Basic Scanning](https://docs.aws.amazon.com/AmazonECR/latest/userguide/image-scanning-basic.html?utm_source=chatgpt.com)
- [Amazon ECR Enhanced Scanning](https://docs.aws.amazon.com/AmazonECR/latest/userguide/image-scanning-enhanced.html?utm_source=chatgpt.com)
- [ECR Repository Policies](https://docs.aws.amazon.com/AmazonECR/latest/userguide/repository-policies.html?utm_source=chatgpt.com)
- [ECR Encryption at Rest](https://docs.aws.amazon.com/AmazonECR/latest/userguide/encryption-at-rest.html?utm_source=chatgpt.com)
- [ECR Lifecycle Policies](https://docs.aws.amazon.com/AmazonECR/latest/userguide/LifecyclePolicies.html?utm_source=chatgpt.com)
- [Deleting an ECR Repository](https://docs.aws.amazon.com/AmazonECR/latest/userguide/repository-delete.html?utm_source=chatgpt.com)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

✅ Module 01 — Amazon ECR Completed (5/5 Labs)

Next: Module 02 — Amazon EKS

Lab 02001 — Amazon EKS Architecture, IAM, VPC & Cluster Prerequisites

We will begin EKS with a Console-first introduction, followed by installation of essential tools and reusable scripts for EKS cluster creation and deletion.
