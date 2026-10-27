# 🧪 Lab 02008 — Configure EKS Access, Kubernetes RBAC & Basic Troubleshooting

Explore Amazon EKS access management, configure namespace-level Kubernetes RBAC, and troubleshoot common cluster issues using the AWS Console and `kubectl`.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🌍 Lab Overview

Amazon EKS uses AWS IAM for authentication and Kubernetes authorization mechanisms to control cluster access.

In this lab, you will explore EKS access entries, create a Kubernetes Role and RoleBinding, test permissions, and practice essential troubleshooting commands.

We will use a dedicated Kubernetes ServiceAccount to demonstrate Role-Based Access Control (RBAC) without creating additional AWS IAM users or roles.

| Property    | Details                           |
| ----------- | --------------------------------- |
| Lab ID      | 02008                             |
| Module      | 02 — Amazon EKS                   |
| Difficulty  | Beginner                          |
| Duration    | 25–30 minutes                     |
| Tools       | AWS Console, kubectl              |
| Environment | Amazon EKS                        |
| Cluster     | `vyomanant-eks`                   |
| Namespace   | `vyomanant-lab`                   |
| Focus       | EKS Access, RBAC, Troubleshooting |

Important: This lab does not create new AWS infrastructure or modify existing EKS administrator permissions.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🎯 Learning Objectives

By the end of this lab, you will be able to:

1. Understand IAM authentication and Kubernetes authorization.
2. Explore EKS access entries through the AWS Console.
3. Identify Kubernetes Roles and RoleBindings.
4. Create a namespace-scoped ServiceAccount.
5. Configure read-only access to Pods.
6. Verify allowed and denied operations.
7. Troubleshoot common Pod and node issues.
8. Remove temporary RBAC resources safely.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧰 Prerequisites

Ensure you have:

- Completed Lab 02007 — Rolling Updates, Rollbacks & Scaling.
- An active EKS cluster named `vyomanant-eks`.
- Two worker nodes in `Ready` state.
- Namespace `vyomanant-lab`.
- NGINX Deployment `nginx-deployment`.
- AWS CLI and kubectl installed.
- Working AWS CLI profile `vyomanant`.
- Kubernetes permissions to create ServiceAccounts, Roles, and RoleBindings and to perform authorization checks.

### 🔎 Verify Cluster Access

```
export AWS_PROFILE=vyomanant
```

```
kubectl get nodes
```

```
kubectl get pods -n vyomanant-lab
```

✅ Expected: Worker nodes are `Ready`, and the NGINX application Pods are running.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🏗️ Lab Architecture

```
              AWS Management Console
                        │
                        ▼
            ┌─────────────────────┐
            │ Amazon EKS          │
            │ vyomanant-eks       │
            │                     │
            │ IAM Authentication  │
            │ EKS Access Entries  │
            └─────────────────────┘


                 Kubernetes RBAC
┌───────────────────────────────────────────────┐
│                                               │
│  Namespace: vyomanant-lab                     │
│                                               │
│  ┌─────────────────────────────────────────┐  │
│  │ ServiceAccount: pod-reader              │  │
│  └────────────────────┬────────────────────┘  │
│                       │                       │
│                       ▼                       │
│  ┌─────────────────────────────────────────┐  │
│  │ RoleBinding: pod-reader-binding         │  │
│  └────────────────────┬────────────────────┘  │
│                       │                       │
│                       ▼                       │
│  ┌─────────────────────────────────────────┐  │
│  │ Role: pod-reader-role                   │  │
│  │                                         │  │
│  │ ALLOW: get, list, watch Pods            │  │
│  │ DENY:  create, delete Pods              │  │
│  └─────────────────────────────────────────┘  │
│                                               │
└───────────────────────────────────────────────┘
```

Architecture explanation:

- AWS IAM authenticates AWS identities accessing EKS.
- EKS access entries can associate IAM principals with cluster access.
- Kubernetes RBAC controls permissions inside Kubernetes.
- The `pod-reader` ServiceAccount receives read-only Pod permissions in `vyomanant-lab`.
- The ServiceAccount cannot create or delete Pods through this Role.

Note: Kubernetes RBAC permissions are additive. The expected denied operations assume no other RoleBindings or ClusterRoleBindings grant additional permissions to this ServiceAccount.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🚀 Step-by-Step Instructions

## 🔹 Step 1 — Explore EKS Access Entries

🎯 Objective: Understand how IAM identities access the Kubernetes API.

### AWS Console Steps

1. Open the [AWS Management Console](https://console.aws.amazon.com/?utm_source=chatgpt.com).
2. Navigate to Amazon EKS → Clusters.
3. Select `vyomanant-eks`.
4. Open the Access tab.
5. Review the cluster's authentication mode.
6. Inspect existing access entries, if available.

### EKS Authentication Modes

| Mode                 | Description                                      |
| -------------------- | ------------------------------------------------ |
| `CONFIG_MAP`         | Uses the legacy `aws-auth` ConfigMap             |
| `API_AND_CONFIG_MAP` | Supports access entries and the legacy ConfigMap |
| `API`                | Uses EKS access entries for IAM principal access |

Important: Access entries are available only when the cluster's authentication mode supports them.

Do not change the authentication mode during this lab.

⚠️ Warning: Incorrect access changes can prevent administrators from accessing the Kubernetes cluster.

✅ Expected: You can identify the cluster authentication mode and review available access entries.

## 🔹 Step 2 — Understand Kubernetes RBAC

🎯 Objective: Identify the components used to authorize Kubernetes operations.

| RBAC Component     | Purpose                                         |
| ------------------ | ----------------------------------------------- |
| Role               | Defines permissions within a namespace          |
| ClusterRole        | Defines permissions that can apply cluster-wide |
| RoleBinding        | Grants permissions within a namespace           |
| ClusterRoleBinding | Grants permissions cluster-wide                 |
| ServiceAccount     | Provides a Kubernetes workload identity         |

### RBAC Permission Example

| Operation         | Permission |
| ----------------- | ---------- |
| View a Pod        | `get`      |
| List Pods         | `list`     |
| Watch Pod changes | `watch`    |
| Create a Pod      | `create`   |
| Delete a Pod      | `delete`   |

For this lab: We will grant only `get`, `list`, and `watch` permissions on Pods.

## 🔹 Step 3 — Create a ServiceAccount

🎯 Objective: Create an identity for RBAC testing.

```
kubectl create serviceaccount pod-reader -n vyomanant-lab
```

✅ Expected:

```
serviceaccount/pod-reader created
```

Verify:

```
kubectl get serviceaccounts -n vyomanant-lab
```

Expected: The `pod-reader` ServiceAccount appears.

🧠 Why It Matters: ServiceAccounts provide identities for Kubernetes workloads and can receive RBAC permissions.

## 🔹 Step 4 — Create a Read-Only Kubernetes Role

🎯 Objective: Define permissions to view Pods.

Create a Role manifest:

```
nano pod-reader-role.yaml
```

Add:

```
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: pod-reader-role
  namespace: vyomanant-lab
rules:
  - apiGroups: [""]
    resources: ["pods"]
    verbs: ["get", "list", "watch"]
```

Save the file.

Apply the Role:

```
kubectl apply -f pod-reader-role.yaml
```

✅ Expected:

```
role.rbac.authorization.k8s.io/pod-reader-role created
```

🧠 Why It Matters: A Role defines what actions are permitted, but it does not grant permissions until bound to an identity.

## 🔹 Step 5 — Create a RoleBinding

🎯 Objective: Assign the Role to the ServiceAccount.

Create the manifest:

```
nano pod-reader-binding.yaml
```

Add:

```
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: pod-reader-binding
  namespace: vyomanant-lab
subjects:
  - kind: ServiceAccount
    name: pod-reader
    namespace: vyomanant-lab
roleRef:
  kind: Role
  name: pod-reader-role
  apiGroup: rbac.authorization.k8s.io
```

Apply:

```
kubectl apply -f pod-reader-binding.yaml
```

✅ Expected:

```
rolebinding.rbac.authorization.k8s.io/pod-reader-binding created
```

### Permission Flow

```
ServiceAccount: pod-reader
           │
           ▼
RoleBinding: pod-reader-binding
           │
           ▼
Role: pod-reader-role
           │
           ▼
Allowed:
get, list, watch Pods
```

## 🔹 Step 6 — Verify RBAC Permissions

🎯 Objective: Test allowed and denied operations.

We will use `kubectl auth can-i` to evaluate permissions for the ServiceAccount.

Important: The `--as` flag impersonates the specified Kubernetes identity for an authorization check. Your current identity must have impersonation permissions to run these commands.

### Test Allowed Permission

```
kubectl auth can-i list pods --as=system:serviceaccount:vyomanant-lab:pod-reader -n vyomanant-lab
```

✅ Expected:

```
yes
```

### Test Denied Permission

```
kubectl auth can-i delete pods --as=system:serviceaccount:vyomanant-lab:pod-reader -n vyomanant-lab
```

✅ Expected:

```
no
```

### Test Pod Creation

```
kubectl auth can-i create pods --as=system:serviceaccount:vyomanant-lab:pod-reader -n vyomanant-lab
```

✅ Expected:

```
no
```

🔎 Observation: The ServiceAccount can read Pods but cannot create or delete them.

Technical Note: These checks evaluate authorization without creating or deleting resources. A `Forbidden` impersonation error means your current identity lacks the required impersonation permission; it does not prove that the ServiceAccount's RBAC configuration is incorrect.

## 🔹 Step 7 — Inspect Kubernetes RBAC Resources

🎯 Objective: Review the Role and RoleBinding.

```
kubectl get roles -n vyomanant-lab
```

```
kubectl get rolebindings -n vyomanant-lab
```

Inspect the Role:

```
kubectl describe role pod-reader-role -n vyomanant-lab
```

✅ Expected: The Role lists the following permissions:

```
Resources   Verbs
pods        get, list, watch
```

🧠 Why It Matters: Inspecting RBAC resources helps diagnose authorization issues.

## 🔹 Step 8 — Practice Basic Kubernetes Troubleshooting

🎯 Objective: Learn essential commands for investigating cluster and application issues.

### Check Worker Nodes

```
kubectl get nodes
```

Expected: Both worker nodes are `Ready`.

### Check Application Pods

```
kubectl get pods -n vyomanant-lab
```

Expected: Two NGINX Pods are running.

### Inspect Pod Details

```
kubectl describe pods -n vyomanant-lab
```

Review Pod conditions, container states, and events.

### Check Application Logs

```
kubectl logs deployment/nginx-deployment -n vyomanant-lab
```

Expected: Available NGINX container logs are displayed.

### Check Namespace Events

```
kubectl get events -n vyomanant-lab
```

Expected: Recent events are displayed, if any are available.

🧠 Why It Matters: These commands help identify scheduling failures, container errors, image pull problems, and application startup issues.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🔎 Verification

| Verification            | Expected Result       |
| ----------------------- | --------------------- |
| EKS authentication mode | Reviewed              |
| EKS access entries      | Reviewed if supported |
| ServiceAccount          | `pod-reader`          |
| Role                    | `pod-reader-role`     |
| RoleBinding             | `pod-reader-binding`  |
| List Pods permission    | `yes`                 |
| Delete Pods permission  | `no`                  |
| Create Pods permission  | `no`                  |
| Worker nodes            | Ready                 |
| NGINX Pods              | Running               |

Lab completion criteria: You understand EKS access management, have configured namespace-level RBAC, and can perform basic Kubernetes troubleshooting.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🛠️ Troubleshooting

| Issue                                 | Possible Cause                             | Resolution                                                 |
| ------------------------------------- | ------------------------------------------ | ---------------------------------------------------------- |
| EKS access entries not visible        | Cluster uses legacy authentication mode    | Review the authentication mode; do not change it           |
| RBAC check returns `Forbidden`        | Missing impersonation permission           | Use an authorized administrator identity                   |
| RBAC check unexpectedly returns `yes` | Another binding grants permissions         | Review all applicable RoleBindings and ClusterRoleBindings |
| Pods show `Pending`                   | Insufficient resources or scheduling issue | Inspect Pod events and node capacity                       |
| Pods show `ImagePullBackOff`          | Container image cannot be pulled           | Verify image name, tag, and registry access                |

### 🔎 Inspect RoleBinding

```
kubectl describe rolebinding pod-reader-binding -n vyomanant-lab
```

### 🔎 Inspect Cluster Events

```
kubectl get events -A
```

Important: Avoid changing cluster-wide RBAC or administrator access during beginner troubleshooting exercises.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧹 Cleanup

🎯 Objective: Remove only the temporary RBAC resources created in this lab.

Delete the RoleBinding:

```
kubectl delete -f pod-reader-binding.yaml
```

Delete the Role:

```
kubectl delete -f pod-reader-role.yaml
```

Delete the ServiceAccount:

```
kubectl delete serviceaccount pod-reader -n vyomanant-lab
```

### 🔎 Verify Cleanup

```
kubectl get serviceaccounts,roles,rolebindings -n vyomanant-lab
```

✅ Expected: The `pod-reader` ServiceAccount, `pod-reader-role`, and `pod-reader-binding` are no longer present.

Other existing Kubernetes resources may still appear.

### Resources to Keep

| Resource                      | Action |
| ----------------------------- | ------ |
| EKS cluster `vyomanant-eks`   | Keep   |
| Managed node group `workers`  | Keep   |
| Namespace `vyomanant-lab`     | Keep   |
| Deployment `nginx-deployment` | Keep   |
| Service `nginx-service`       | Keep   |

⚠️ Cost Reminder: The EKS cluster and worker nodes continue generating AWS charges.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 🧠 Key Takeaways

1. AWS IAM authenticates AWS identities accessing EKS.
2. EKS access entries provide an AWS-managed mechanism for cluster access.
3. Kubernetes RBAC controls permissions on Kubernetes resources.
4. Roles define namespace-scoped permissions.
5. RoleBindings grant permissions to users, groups, or ServiceAccounts.
6. ServiceAccounts provide identities for Kubernetes workloads.
7. `kubectl auth can-i` helps verify Kubernetes authorization.
8. `kubectl describe`, `kubectl logs`, and events support troubleshooting.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# 📚 Official Documentation

- [Amazon EKS Access Entries](https://docs.aws.amazon.com/eks/latest/userguide/access-entries.html?utm_source=chatgpt.com)
- [Amazon EKS Cluster Authentication Modes](https://docs.aws.amazon.com/eks/latest/userguide/grant-k8s-access.html?utm_source=chatgpt.com)
- [Kubernetes RBAC Authorization](https://kubernetes.io/docs/reference/access-authn-authz/rbac/?utm_source=chatgpt.com)
- [Kubernetes ServiceAccounts](https://kubernetes.io/docs/concepts/security/service-accounts/?utm_source=chatgpt.com)
- [kubectl Auth Can-I](https://kubernetes.io/docs/reference/kubectl/generated/kubectl_auth/kubectl_auth_can-i/?utm_source=chatgpt.com)
- [Debugging Kubernetes Pods](https://kubernetes.io/docs/tasks/debug/debug-application/debug-pods/?utm_source=chatgpt.com)
- [Amazon EKS Troubleshooting](https://docs.aws.amazon.com/eks/latest/userguide/troubleshooting.html?utm_source=chatgpt.com)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Next Lab: 02009 — Delete an Amazon EKS Cluster Using a Reusable Shell Script

We will create a reusable EKS deletion script, review the safe cleanup sequence, and learn how to remove EKS infrastructure without leaving unnecessary AWS resources.

Important: We will prepare and verify the deletion script, but retain the EKS cluster until the Module 03 integration labs are completed.
