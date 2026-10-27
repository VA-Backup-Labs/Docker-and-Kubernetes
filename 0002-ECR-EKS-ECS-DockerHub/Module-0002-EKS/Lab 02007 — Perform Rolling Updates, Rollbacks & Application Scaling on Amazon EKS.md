# 🧪 Lab 02007 — Perform Rolling Updates, Rollbacks & Application Scaling on Amazon EKS

Learn how to update container images, roll back deployments, and scale applications on Amazon EKS using simple `kubectl` commands.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🌍 Lab Overview

Kubernetes Deployments help manage application updates without manually deleting and recreating Pods.

In this lab, you will update the existing NGINX application, observe a rolling update, restore its previous version using a rollback, and scale the application from two to three replicas.

| Property         | Details              |
| ---------------- | -------------------- |
| Lab ID           | 02007                |
| Module           | 02 — Amazon EKS      |
| Difficulty       | Beginner             |
| Duration         | 20–25 minutes        |
| Tools            | kubectl, AWS Console |
| Environment      | Amazon EKS           |
| Cluster          | `vyomanant-eks`      |
| Namespace        | `vyomanant-lab`      |
| Application      | NGINX                |
| Initial Replicas | 2                    |
| Scaling Target   | 3                    |

Important: This lab reuses the existing Deployment and ClusterIP Service. No new AWS load balancer is created.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🎯 Learning Objectives

By the end of this lab, you will be able to:

1. Inspect an existing Kubernetes Deployment.
2. Understand rolling updates.
3. Update an application's container image.
4. Monitor Deployment rollout status.
5. Review Deployment revision history.
6. Roll back to a previous application version.
7. Scale an application up and down.
8. Verify the desired and available replica counts.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧰 Prerequisites

Ensure you have:

- Completed Lab 02006 — Expose EKS Applications Using ClusterIP & LoadBalancer Services.
- An active EKS cluster named `vyomanant-eks`.
- Namespace `vyomanant-lab`.
- Deployment `nginx-deployment`.
- ClusterIP Service `nginx-service`.
- AWS CLI and kubectl configured.

### 🔎 Verify Cluster Access

```
export AWS_PROFILE=vyomanant
```

```
kubectl get nodes
```

✅ Expected: Two worker nodes are in the `Ready` state.

Check the application:

```
kubectl get deployments -n vyomanant-lab
```

✅ Expected: `nginx-deployment` has two available replicas.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🏗️ Lab Architecture

```
                Amazon EKS
             Cluster: vyomanant-eks
┌────────────────────────────────────────────┐
│                                            │
│  Namespace: vyomanant-lab                  │
│                                            │
│  Deployment: nginx-deployment              │
│                                            │
│  Initial State                             │
│  ┌────────────┐    ┌────────────┐          │
│  │ NGINX Pod  │    │ NGINX Pod  │          │
│  │ nginx:1.28 │    │ nginx:1.28 │          │
│  └────────────┘    └────────────┘          │
│          │                │                │
│          └────────┬───────┘                │
│                   ▼                        │
│         Rolling Image Update               │
│                   │                        │
│                   ▼                        │
│  ┌────────────┐    ┌────────────┐          │
│  │ NGINX Pod  │    │ NGINX Pod  │          │
│  │ nginx:1.29 │    │ nginx:1.29 │          │
│  └────────────┘    └────────────┘          │
│                   │                        │
│                   ▼                        │
│           Rollback to 1.28                 │
│                   │                        │
│                   ▼                        │
│           Scale: 2 → 3 → 2                 │
│                                            │
│  Service: nginx-service (ClusterIP)        │
└────────────────────────────────────────────┘
```

Architecture explanation:

- The Deployment manages the NGINX Pods.
- A rolling update gradually replaces old Pods with new Pods.
- A rollback restores the previous Deployment template.
- Scaling changes the number of desired Pod replicas.
- The existing ClusterIP Service continues selecting Pods labeled `app: nginx`.

Note: Kubernetes may temporarily create extra Pods during rolling updates.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🚀 Step-by-Step Instructions

## 🔹 Step 1 — Inspect the Existing Deployment

🎯 Objective: Review the current application state.

```
kubectl get deployment nginx-deployment -n vyomanant-lab
```

✅ Expected (illustrative):

```
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
nginx-deployment   2/2     2            2           30m
```

View the current Pods:

```
kubectl get pods -n vyomanant-lab
```

🔎 Verify: Two NGINX Pods are running.

## 🔹 Step 2 — Understand Rolling Updates

🎯 Objective: Learn how Kubernetes updates applications.

A rolling update gradually replaces existing Pods with Pods running a new container image.

### Deployment Strategies

| Strategy      | Description                                  |
| ------------- | -------------------------------------------- |
| RollingUpdate | Gradually replaces old Pods                  |
| Recreate      | Terminates old Pods before creating new Pods |

RollingUpdate is the default Deployment strategy.

### Why Rolling Updates Matter

- Reduce application downtime.
- Gradually introduce new application versions.
- Allow rollout progress monitoring.
- Support rollback to earlier Deployment revisions.

Important: Zero downtime is not automatically guaranteed. Application readiness, available capacity, and Deployment configuration affect availability.

## 🔹 Step 3 — Update the NGINX Container Image

🎯 Objective: Change the application image from `nginx:1.28` to `nginx:1.29`.

Run:

```
kubectl set image deployment/nginx-deployment nginx=nginx:1.29 -n vyomanant-lab
```

Command explanation:

- `set image` updates a container image.
- `deployment/nginx-deployment` identifies the Deployment.
- `nginx=nginx:1.29` updates the container named `nginx`.
- `-n` selects the namespace.

✅ Expected:

```
deployment.apps/nginx-deployment image updated
```

Technical Note: This lab assumes `nginx:1.29` is available from the container registry. If that image tag cannot be pulled, the rollout will fail; inspect the Pods and roll back rather than assuming the tag is available.

## 🔹 Step 4 — Monitor the Rolling Update

🎯 Objective: Observe the Deployment rollout.

```
kubectl rollout status deployment/nginx-deployment -n vyomanant-lab --timeout=180s
```

✅ Expected:

```
deployment "nginx-deployment" successfully rolled out
```

Check the Pods:

```
kubectl get pods -n vyomanant-lab
```

Expected: Two running NGINX Pods with newly generated Pod names.

🧠 Why It Matters: Kubernetes replaces the old ReplicaSet's Pods with Pods created from the updated Deployment template.

## 🔹 Step 5 — Review Deployment Revision History

🎯 Objective: Identify previous Deployment revisions.

```
kubectl rollout history deployment/nginx-deployment -n vyomanant-lab
```

Expected (illustrative):

```
deployment.apps/nginx-deployment
REVISION  CHANGE-CAUSE
1         <none>
2         <none>
```

### Understanding Revisions

| Revision | Application Image |
| -------- | ----------------- |
| 1        | `nginx:1.28`      |
| 2        | `nginx:1.29`      |

Note: The revision-to-image mapping assumes the Deployment was created in Lab 02005 and has not undergone other Pod template changes. Kubernetes does not automatically display image tags in this history table.

## 🔹 Step 6 — Roll Back the Deployment

🎯 Objective: Restore the previous application version.

```
kubectl rollout undo deployment/nginx-deployment -n vyomanant-lab
```

✅ Expected:

```
deployment.apps/nginx-deployment rolled back
```

Verify the rollback:

```
kubectl rollout status deployment/nginx-deployment -n vyomanant-lab --timeout=180s
```

Expected: The Deployment successfully rolls out the previous Pod template using `nginx:1.28`.

🧠 Why It Matters: Rollbacks provide a recovery option when a new application version causes problems.

## 🔹 Step 7 — Scale the Application to Three Replicas

🎯 Objective: Increase the number of NGINX Pods.

```
kubectl scale deployment nginx-deployment --replicas=3 -n vyomanant-lab
```

✅ Expected:

```
deployment.apps/nginx-deployment scaled
```

Verify:

```
kubectl get deployments -n vyomanant-lab
```

Expected (after Pods become ready):

```
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
nginx-deployment   3/3     3            3           45m
```

Important: Three replicas do not require three worker nodes. Kubernetes can schedule multiple Pods on the same node when sufficient resources are available.

## 🔹 Step 8 — Scale Back to Two Replicas

🎯 Objective: Restore the original application replica count.

```
kubectl scale deployment nginx-deployment --replicas=2 -n vyomanant-lab
```

Verify:

```
kubectl get pods -n vyomanant-lab
```

✅ Expected: Two NGINX Pods remain running after scaling completes.

🧠 Why It Matters: Scaling allows Kubernetes applications to adapt to changing workload requirements.

## 🔹 Step 9 — Verify the Final Deployment

🎯 Objective: Confirm that the application is restored to its original state.

```
kubectl describe deployment nginx-deployment -n vyomanant-lab
```

Review the following:

| Setting             | Expected           |
| ------------------- | ------------------ |
| Deployment          | `nginx-deployment` |
| Namespace           | `vyomanant-lab`    |
| Container image     | `nginx:1.28`       |
| Desired replicas    | 2                  |
| Available replicas  | 2                  |
| Deployment strategy | RollingUpdate      |

Important: The original `nginx-deployment.yaml` still declares `nginx:1.28` and two replicas. After the rollback and scaling operations, the live Deployment should match these original values.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🔎 Verification

| Check              | Expected Result |
| ------------------ | --------------- |
| Initial Deployment | 2/2 Ready       |
| Image update       | Initiated       |
| Rolling update     | Successful      |
| Revision history   | Available       |
| Rollback           | Successful      |
| Scale up           | 3 replicas      |
| Scale down         | 2 replicas      |
| Final image        | `nginx:1.28`    |
| Final replicas     | 2               |

Lab completion criteria: You successfully updated NGINX, monitored the rollout, restored the previous image, and scaled the Deployment up and down.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🛠️ Troubleshooting

| Issue                              | Possible Cause                                               | Resolution                                         |
| ---------------------------------- | ------------------------------------------------------------ | -------------------------------------------------- |
| `ImagePullBackOff`                 | Image tag unavailable or registry access issue               | Inspect Pod events and roll back                   |
| Rollout remains incomplete         | New Pods are not becoming ready                              | Check Deployment status and Pod details            |
| Rollback fails                     | Previous revision unavailable                                | Review rollout history                             |
| Scaling Pods remain `Pending`      | Insufficient node capacity                                   | Inspect Pod events and node resources              |
| Replica count changes unexpectedly | Another controller or automation is modifying the Deployment | Review Deployment events and scaling configuration |

### 🔎 Inspect Deployment Status

```
kubectl describe deployment nginx-deployment -n vyomanant-lab
```

### 🔎 Inspect Pod Problems

```
kubectl describe pods -n vyomanant-lab
```

### 🔎 Recover from an Unsuccessful Update

```
kubectl rollout undo deployment/nginx-deployment -n vyomanant-lab
```

Note: If the update never created a new Deployment revision, there may be no new revision to undo.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧹 Cleanup

No resource deletion is required.

The application has been restored to:

- Image: `nginx:1.28`
- Replicas: `2`
- Service: `nginx-service`
- Namespace: `vyomanant-lab`

Keep the resources for the remaining EKS labs.

| Resource                          | Action |
| --------------------------------- | ------ |
| EKS cluster `vyomanant-eks`       | Keep   |
| Managed node group `workers`      | Keep   |
| Namespace `vyomanant-lab`         | Keep   |
| Deployment `nginx-deployment`     | Keep   |
| ClusterIP Service `nginx-service` | Keep   |

⚠️ Cost Reminder: The EKS cluster and EC2 worker nodes continue generating AWS charges while running.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧠 Key Takeaways

1. Kubernetes Deployments support controlled application updates.
2. Rolling updates gradually replace existing Pods.
3. `kubectl set image` changes the Deployment's container image.
4. `kubectl rollout status` monitors rollout progress.
5. `kubectl rollout history` displays Deployment revisions.
6. `kubectl rollout undo` restores a previous Deployment template.
7. `kubectl scale` changes the desired replica count.
8. Kubernetes can run multiple application Pods on the same worker node.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 📚 Official Documentation

- [Kubernetes Deployments](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/?utm_source=chatgpt.com)
- [Rolling Updates](https://kubernetes.io/docs/tutorials/kubernetes-basics/update/update-intro/?utm_source=chatgpt.com)
- [kubectl Set Image](https://kubernetes.io/docs/reference/kubectl/generated/kubectl_set/kubectl_set_image/?utm_source=chatgpt.com)
- [kubectl Rollout](https://kubernetes.io/docs/reference/kubectl/generated/kubectl_rollout/?utm_source=chatgpt.com)
- [kubectl Rollout Undo](https://kubernetes.io/docs/reference/kubectl/generated/kubectl_rollout/kubectl_rollout_undo/?utm_source=chatgpt.com)
- [kubectl Scale](https://kubernetes.io/docs/reference/kubectl/generated/kubectl_scale/?utm_source=chatgpt.com)
- [Amazon EKS Documentation](https://docs.aws.amazon.com/eks/latest/userguide/what-is-eks.html?utm_source=chatgpt.com)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Next Lab: 02008 — Configure EKS Access, Kubernetes RBAC & Basic Troubleshooting

We will explore EKS access entries, create a namespace-scoped Kubernetes Role and RoleBinding, verify permissions, and practice essential cluster troubleshooting commands.
