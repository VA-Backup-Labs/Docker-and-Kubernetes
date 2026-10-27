# 🧪 Lab 01004 — Manage ECR Image Tags, Versions & Lifecycle Policies

Learn to manage Docker image versions, configure image tag immutability, and automatically clean up older images using Amazon ECR lifecycle policies.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🌍 Lab Overview

Amazon ECR allows you to manage container image versions using image tags and automate image cleanup using lifecycle policies.

In this lab, you will reuse the `myapp` repository from previous labs and perform image management using the AWS Management Console.

| Property    | Details                    |
| ----------- | -------------------------- |
| Lab ID      | 01004                      |
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

1. View existing Docker images in an ECR repository.
2. Understand image tags and image digests.
3. Identify differences between mutable and immutable tags.
4. Configure image tag immutability.
5. Create an ECR lifecycle policy.
6. Preview lifecycle policy actions.
7. Understand automated image retention.
8. Verify repository image management settings.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧰 Prerequisites

Ensure you have:

- Completed Lab 01003 — Build, Tag, Push & Pull Docker Images Using Amazon ECR.
- An existing private ECR repository named `myapp`.
- At least one image tagged `v1`.
- AWS Console access.
- IAM permissions to view and modify ECR repository settings and lifecycle policies.

Important: This lab uses AWS Console only. No Docker CLI or AWS CLI commands are required.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🏗️ Lab Architecture

```
┌─────────────────────────────────┐
│       AWS Management Console    │
└────────────────┬────────────────┘
                 │
                 ▼
┌─────────────────────────────────┐
│           Amazon ECR            │
│                                 │
│  Repository: myapp              │
│                                 │
│  ┌───────────────────────────┐  │
│  │ Container Images          │  │
│  │                           │  │
│  │ myapp:v1                  │  │
│  └───────────────────────────┘  │
│                 │               │
│                 ▼               │
│  ┌───────────────────────────┐  │
│  │ Image Tag Immutability    │  │
│  │ Prevent Tag Overwriting   │  │
│  └───────────────────────────┘  │
│                 │               │
│                 ▼               │
│  ┌───────────────────────────┐  │
│  │ Lifecycle Policy          │  │
│  │ Retain Recent Images      │  │
│  │ Expire Older Images       │  │
│  └───────────────────────────┘  │
└─────────────────────────────────┘
```

Architecture explanation: The existing `myapp` repository contains the `v1` image. Repository settings control tag overwriting, while lifecycle policies determine which images are eligible for automatic expiration.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🚀 Step-by-Step Instructions

## 🔹 Step 1 — Open the Existing ECR Repository

🎯 Objective: Locate the repository created in previous labs.

1. Sign in to the [AWS Management Console](https://console.aws.amazon.com/?utm_source=chatgpt.com).
2. Search for Elastic Container Registry.
3. Navigate to Private registry → Repositories.
4. Select the repository named `myapp`.
5. Open the image list.

🔎 Verify: Locate the image tagged `v1`.

✅ Expected: The repository contains the Docker image uploaded during Lab 01003.

## 🔹 Step 2 — Explore Image Tags and Digests

🎯 Objective: Understand how ECR identifies container images.

Select the `v1` image and review its details.

| Property     | Description                         |
| ------------ | ----------------------------------- |
| Image tag    | Human-readable version such as `v1` |
| Image digest | SHA-256 content identifier          |
| Image URI    | Registry, repository, and image tag |
| Image size   | Stored image size reported by ECR   |
| Pushed at    | Image upload timestamp              |

### Example Image URI

```
123456789012.dkr.ecr.ap-south-1.amazonaws.com/myapp:v1
```

### Example Image Digest

```
sha256:abcdef1234567890...
```

Important: Image tags can reference different image contents over time when tags are mutable. A digest identifies a specific image manifest.

✅ Expected: You can distinguish an image tag from an image digest.

## 🔹 Step 3 — Configure Image Tag Immutability

🎯 Objective: Prevent existing image tags from being overwritten.

1. Navigate to ECR → Private registry → Repositories.
2. Select `myapp`.
3. Locate the repository configuration or edit settings.
4. Find Image tag mutability.
5. Change the setting from Mutable to Immutable.
6. Save the changes.

### Image Tag Behavior

| Setting                   | Behavior                            |
| ------------------------- | ----------------------------------- |
| Mutable                   | Existing tags can be overwritten    |
| Immutable                 | Existing tags cannot be overwritten |
| Immutable with exclusions | Selected tags may be overwritten    |

✅ Expected: The repository is configured with immutable image tags.

🧠 Why It Matters: Immutability helps prevent accidental replacement of released image versions.

## 🔹 Step 4 — Understand Image Versioning

🎯 Objective: Understand how multiple application versions are stored.

A typical ECR repository may contain:

```
myapp:v1
myapp:v2
myapp:v3
myapp:v4
```

These tags are illustrative. Only `v1` is required to exist in this lab.

### Recommended Versioning Practices

| Tag      | Example Purpose                  |
| -------- | -------------------------------- |
| `v1`     | Initial application release      |
| `v2`     | Updated application release      |
| `v3`     | New application release          |
| `latest` | Moving reference, when permitted |

Production Best Practice: Use unique image tags, such as release versions or Git commit identifiers, for traceable deployments.

✅ Expected: You understand how versioned images support application updates and rollbacks.

## 🔹 Step 5 — Create an ECR Lifecycle Policy

🎯 Objective: Configure automatic expiration of older images.

1. Open the `myapp` repository.
2. Navigate to Lifecycle policy.
3. Choose Create rule or the equivalent lifecycle policy editor.
4. Configure the following settings.

| Setting          | Value                            |
| ---------------- | -------------------------------- |
| Rule priority    | `1`                              |
| Rule description | `Keep latest 3 versioned images` |
| Image status     | Tagged                           |
| Tag selection    | Prefix                           |
| Tag prefix       | `v`                              |
| Count type       | Image count                      |
| Count number     | `3`                              |
| Action           | Expire                           |

5. Review the rule.
6. Save the lifecycle policy.

Note: The ECR Console may present these options using slightly different labels.

⚠️ Warning: This policy will eventually expire older matching images when more than three qualifying images exist. Use it only for this training repository.

🧠 Why It Matters: Lifecycle policies reduce unused image accumulation and help manage storage costs.

## 🔹 Step 6 — Preview the Lifecycle Policy

🎯 Objective: Review which images are eligible for expiration.

1. Open the repository's Lifecycle policy section.
2. Locate the Preview or Test rules option.
3. Run the lifecycle policy preview.
4. Review the results.

### Example Retention Behavior

Assume the repository contains four separately pushed versioned images:

| Image      | Expected Result         |
| ---------- | ----------------------- |
| `myapp:v4` | Retained                |
| `myapp:v3` | Retained                |
| `myapp:v2` | Retained                |
| `myapp:v1` | Eligible for expiration |

Important: This table is illustrative and assumes the images are ordered from newest to oldest by push time. ECR lifecycle policies use image metadata and rule evaluation, not numeric tag ordering.

With only the existing `v1` image, no expiration is expected.

✅ Expected: The lifecycle policy preview shows no images eligible for expiration when only `v1` exists.

## 🔹 Step 7 — Verify Repository Configuration

🎯 Objective: Confirm the repository settings.

Return to the `myapp` repository and verify:

| Configuration        | Expected          |
| -------------------- | ----------------- |
| Repository           | `myapp`           |
| Repository type      | Private           |
| Image tag mutability | Immutable         |
| Existing image       | `v1`              |
| Lifecycle policy     | Configured        |
| Retention count      | 3 matching images |
| Lifecycle action     | Expire            |

✅ Expected: Image immutability and lifecycle policy settings are saved successfully.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🔎 Verification

| Check                     | Expected Result |
| ------------------------- | --------------- |
| ECR repository accessible | Yes             |
| Existing image visible    | `v1`            |
| Image digest visible      | Yes             |
| Image tag immutability    | Enabled         |
| Lifecycle policy          | Created         |
| Lifecycle preview         | Completed       |
| Existing image retained   | Yes             |

Lab completion criteria: You have configured immutable image tags and a lifecycle policy for the `myapp` repository.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🛠️ Troubleshooting

| Issue                                    | Possible Cause                  | Resolution                                        |
| ---------------------------------------- | ------------------------------- | ------------------------------------------------- |
| Repository not found                     | Incorrect AWS Region            | Select the Region used in previous labs           |
| Unable to modify repository              | Missing IAM permissions         | Request appropriate ECR configuration permissions |
| Lifecycle policy cannot be saved         | Invalid rule configuration      | Verify prefix, count, and rule priority           |
| Preview shows no expired images          | Fewer than four matching images | This is expected for the current lab              |
| Existing image tag cannot be overwritten | Image immutability enabled      | Push a new image using a unique tag               |

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧹 Cleanup

Do not delete the `myapp` repository or the `v1` image.

These resources will be reused in upcoming labs.

### Optional Configuration Cleanup

If you do not want the lifecycle policy to remain active:

1. Open Amazon ECR → Private registry → Repositories.
2. Select `myapp`.
3. Open Lifecycle policy.
4. Delete the lab-created lifecycle rule.
5. Confirm the change.

Important: Do not delete existing images unless they are no longer required.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧠 Key Takeaways

1. ECR image tags provide human-readable version identifiers.
2. Image digests identify specific image manifests.
3. Mutable tags can be overwritten.
4. Immutable tags prevent accidental image tag replacement.
5. Lifecycle policies automate image expiration.
6. Image count rules retain a configured number of matching images.
7. Lifecycle previews help identify images eligible for expiration.
8. Image versioning supports safer deployment and rollback workflows.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 📚 Official Documentation

- [Amazon ECR Private Repositories](https://docs.aws.amazon.com/AmazonECR/latest/userguide/Repositories.html?utm_source=chatgpt.com)
- [ECR Image Tag Mutability](https://docs.aws.amazon.com/AmazonECR/latest/userguide/image-tag-mutability.html?utm_source=chatgpt.com)
- [ECR Lifecycle Policies](https://docs.aws.amazon.com/AmazonECR/latest/userguide/LifecyclePolicies.html?utm_source=chatgpt.com)
- [ECR Lifecycle Policy Examples](https://docs.aws.amazon.com/AmazonECR/latest/userguide/lifecycle_policy_examples.html?utm_source=chatgpt.com)
- [ECR Image Management](https://docs.aws.amazon.com/AmazonECR/latest/userguide/viewing_images.html?utm_source=chatgpt.com)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Next Lab: 01005 — Amazon ECR Image Scanning, Repository Security & Cleanup
