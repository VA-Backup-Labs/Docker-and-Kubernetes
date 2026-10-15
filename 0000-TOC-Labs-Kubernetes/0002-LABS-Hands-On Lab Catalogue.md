
# Kubernetes Administration, Engineering & Production Troubleshooting

**Master Hands-On Lab Catalogue | Version 2.4**

**Learning Path:** Beginner → Intermediate → Advanced → Production Engineering  
**Lab Series:** Module-Based Numbering (0000–13003)  
**Total Labs:** 87  
**Total Modules:** 14  
**Cluster Setup Guides:** 7  
**Delivery Mode:** Instructor-Led | Hands-On | Self-Paced | Corporate Training

---

# 🌍 Lab Catalogue Overview

Welcome to the **Vyomanant Academy Kubernetes Administration, Engineering & Production Troubleshooting Hands-On Lab Series**.

This practical learning program helps learners build real-world Kubernetes skills through structured exercises covering:

- Kubernetes Fundamentals and Architecture
- Kubernetes Cluster Installation and Configuration
- kubectl and YAML Resource Management
- Pods, Deployments and Workload Management
- Application Configuration and Secrets
- Kubernetes Networking and Services
- Persistent Storage and Stateful Applications
- Kubernetes Security and RBAC
- Scheduling, Autoscaling and High Availability
- Helm, Kustomize, CRDs and GitOps
- Kubernetes Cluster Administration
- Monitoring and Production Troubleshooting
- Production Operations and Capstone Project

**Training Objective:** Develop practical skills to deploy, configure, administer, secure, monitor and troubleshoot Kubernetes environments.

**Training Approach:**

- Beginner-friendly, step-by-step practical learning
- Real-world Kubernetes administration scenarios
- AWS EC2 Ubuntu and local Ubuntu environments
- Single-node and multi-node Kubernetes options
- Instructor demonstrations and independent practice
- Production-oriented troubleshooting exercises
- Practical validation after major learning activities
- Final integrated Kubernetes capstone project

---

# 🔢 Lab Numbering Convention

Every lab uses a unique identifier based on its module number and sequence.

| Module | Lab Numbering | Example |
|---|---|---|
| Module 00 | 0000–0001 | 0000-Verify-Linux-Prerequisites.md |
| Module 01 | 1001–1002 | 1001-Understand-Kubernetes-Cluster-Architecture-and-Components.md |
| Module 02 | 2001–2008 | 2001-Install-Single-Node-K3s-Kubernetes-on-Ubuntu.md |
| Module 03 | 3001–3005 | 3001-Master-Essential-Kubectl-Commands.md |
| Module 04 | 4001–4009 | 4001-Create-and-Manage-Kubernetes-Pods.md |
| Module 05 | 5001–5004 | 5001-Configure-Applications-Using-ConfigMaps.md |
| Module 06 | 6001–6007 | 6001-Explore-Pod-to-Pod-Network-Communication.md |
| Module 07 | 7001–7006 | 7001-Configure-EmptyDir-and-HostPath-Volumes.md |
| Module 08 | 8001–8007 | 8001-Create-and-Manage-Kubernetes-ServiceAccounts.md |
| Module 09 | 9001–9008 | 9001-Schedule-Pods-Using-NodeSelector-and-Node-Affinity.md |
| Module 10 | 10001–10006 | 10001-Install-and-Manage-Applications-Using-Helm.md |
| Module 11 | 11001–11005 | 11001-Administer-Kubeadm-Control-Plane-and-Worker-Nodes.md |
| Module 12 | 12001–12015 | 12001-Monitor-Kubernetes-Resources-Using-Kubectl-Top.md |
| Module 13 | 13001–13003 | 13001-Deploy-Production-Oriented-MultiTier-Application.md |

**Naming Standards:**

- Every lab receives a unique module-based lab ID.
- Every lab is maintained as an individual Markdown file.
- Lab filenames begin with the assigned lab ID.
- Use descriptive names separated by hyphens.
- Keep labs organized inside their respective module directories.
- Preserve existing lab IDs when adding new labs.
- Newly introduced labs receive the next available ID within their module.

**Example Filename:**

`4001-Create-and-Manage-Kubernetes-Pods.md`

---

# 🧰 Standard Lab Environment

| Component | Recommended Environment |
|---|---|
| Primary Operating System | Ubuntu Server 26.04 LTS |
| Alternative Operating System | Ubuntu Server 24.04 LTS |
| Cloud Infrastructure | AWS EC2 Ubuntu |
| Local Infrastructure | Ubuntu VM / Physical Ubuntu Machine |
| Remote SSH Client | MobaXterm for Windows |
| Text Editor | vi |
| Kubernetes CLI | kubectl |
| Kubernetes Deployment | Single-Node / Multi-Node |
| Configuration Format | YAML |
| Lab Documentation | GitHub Markdown |
| Container Runtime | containerd / Distribution-Managed Runtime |

**Important Notes:**

- Ubuntu 26.04 LTS is the preferred baseline, subject to compatibility with the selected Kubernetes installation method.
- Ubuntu 24.04 LTS can be used when a selected tool or Kubernetes distribution does not yet support Ubuntu 26.04 LTS.
- AWS EC2 Ubuntu and local Ubuntu are the primary infrastructure environments.
- Managed Kubernetes services such as EKS and AKS may require provider-specific operating systems and infrastructure.
- Use `vi` for all interactive file editing.
- Use MobaXterm for remote SSH access from Windows systems.
- Kubernetes version and infrastructure requirements will be specified in individual labs.
- Certain labs require multiple nodes, administrative permissions or additional Kubernetes controllers.
- Always clean up unused AWS and Azure resources to prevent unnecessary charges.

---

# 🏗️ Kubernetes Lab Environment Options

Learners can select an appropriate Kubernetes environment based on infrastructure availability, hardware capacity and learning objectives.

# 🟢 Option A — Single-Node Kubernetes

**Recommended for beginners and learners with limited resources.**

**Supported Setup Choices:**

- K3s on Ubuntu
- Minikube on Ubuntu
- kind on Ubuntu

**Recommended Infrastructure:**

- Local Ubuntu machine
- Ubuntu virtual machine
- AWS EC2 Ubuntu instance with suitable resources

**Suitable For:**

- Kubernetes Fundamentals
- kubectl and YAML
- Pods and Deployments
- ConfigMaps and Secrets
- Kubernetes Services
- Basic Persistent Storage
- RBAC and Security
- Helm and Kustomize
- Application Monitoring
- Workload Troubleshooting

# 🔵 Option B — Multi-Node Kubernetes

**Recommended for cloud engineering and advanced Kubernetes administration.**

**Supported Setup Choices:**

- kOps on AWS
- Amazon EKS
- Azure AKS

**Recommended Infrastructure:**

- AWS Cloud
- Microsoft Azure
- Multiple Kubernetes worker nodes

**Suitable For:**

- Multi-Node Scheduling
- Node Affinity and Anti-Affinity
- Taints and Tolerations
- Node Maintenance
- Workload High Availability
- PodDisruptionBudgets
- Cluster and Node Autoscaling
- Cloud Networking
- Advanced Production Operations

# 🟣 Option C — Self-Managed Kubernetes Using kubeadm

**Recommended for advanced cluster administration.**

**Supported Setup:**

- kubeadm on Ubuntu

**Recommended Infrastructure:**

- Multiple Ubuntu virtual machines
- AWS EC2 Ubuntu instances
- Dedicated Linux infrastructure

**Suitable For:**

- Control-Plane Installation
- Worker-Node Joining
- Kubernetes Certificates
- kubelet Administration
- containerd Configuration
- CNI Installation
- Kubernetes Upgrades
- etcd Backup and Restore
- Control-Plane Troubleshooting

**Important:** Learners need to complete only one suitable cluster installation path to begin the core Kubernetes labs.

---

# 🖥️ Lab Environment Compatibility

Every hands-on lab should clearly identify its supported execution environment.

| Classification | Description |
|---|---|
| Cluster-Independent | No Kubernetes cluster is required |
| Single-Node Compatible | Can run on a suitable single-node Kubernetes cluster |
| Multi-Node Required | Requires multiple schedulable Kubernetes nodes |
| Self-Managed Cluster Required | Requires control-plane or node-level administrative access |
| Cloud-Provider-Specific | Requires AWS, Azure or another supported cloud integration |
| Additional Components Required | Requires controllers, metrics services, CSI drivers or other add-ons |

**Environment Guidelines:**

- Module 00 uses Linux infrastructure without requiring Kubernetes.
- Module 01 consists of architecture and conceptual exercises that do not require a running Kubernetes cluster.
- Module 02 installs and validates the Kubernetes environment.
- Prefer single-node environments for beginner and intermediate workload labs.
- Use multi-node clusters for node scheduling and availability exercises.
- Use kubeadm-based Ubuntu clusters for advanced administrative exercises.
- Use managed Kubernetes environments for cloud-specific integration exercises.
- Validate platform compatibility before installing additional controllers or extensions.
- Perform disruptive troubleshooting exercises only on dedicated lab clusters.

---

# 🎯 Learning Path

| Phase | Modules | Learning Focus |
|---|---|---|
| Phase 1 | 00–02 | Prerequisites, Kubernetes Fundamentals and Cluster Setup |
| Phase 2 | 03–05 | kubectl, YAML, Pods, Deployments and Configuration |
| Phase 3 | 06–08 | Networking, Storage and Security |
| Phase 4 | 09–10 | Scheduling, Autoscaling, Helm and GitOps |
| Phase 5 | 11–12 | Cluster Administration and Production Troubleshooting |
| Phase 6 | 13 | Production Operations and Capstone Project |

---

# 📚 Complete Hands-On Lab Catalogue

# 📁 Module 00 — Kubernetes Orientation & Prerequisites

**Level:** Foundation  
**Total Labs:** 2  
**Kubernetes Cluster Required:** No  
**Delivery:** Linux Hands-On | Environment Preparation

- **Lab 0000:** Verify Linux, Container and Networking Prerequisites
- **Lab 0001:** Prepare Kubernetes Lab Workstation

**Learning Outcome:** Prepare a local Ubuntu or AWS EC2 Ubuntu environment with the essential tools and prerequisites for Kubernetes training.

**Recommended Environment:** Local Ubuntu / AWS EC2 Ubuntu

**Important:** Kubernetes installation is not required for either lab in Module 00.

---

# 📁 Module 01 — Kubernetes Fundamentals & Architecture

**Level:** Beginner  
**Total Labs:** 2  
**Kubernetes Cluster Required:** No  
**Delivery:** Theory | Architecture Diagrams | Guided Conceptual Exercises

- **Lab 1001:** Understand Kubernetes Cluster Architecture and Components
- **Lab 1002:** Explore Kubernetes API, Desired State and Reconciliation Concepts

**Learning Outcome:** Explain Kubernetes architecture, control-plane components, worker nodes, API interactions and reconciliation without requiring a running cluster.

# Lab 1001 — Guided Activities

- Identify control-plane and worker-node components.
- Understand each component's responsibilities.
- Trace how Kubernetes processes a deployment request.
- Explore architecture and communication diagrams.
- Identify the roles of the API server, etcd, scheduler, controller manager, kubelet and container runtime.

# Lab 1002 — Guided Activities

- Examine sample Kubernetes YAML manifests.
- Identify apiVersion, kind, metadata and spec.
- Understand desired state vs observed state.
- Explore controller reconciliation using illustrated examples.
- Understand declarative and imperative management concepts.
- Explain Kubernetes API request processing at a conceptual level.

**Important:** No Kubernetes cluster, kubectl commands, running Pods or live API access are required in Module 01.

Actual cluster installation begins in Module 02.

---

# 📁 Module 02 — Kubernetes Cluster Installation & Lab Environment Setup

**Level:** Beginner to Advanced  
**Total Labs:** 8  
**Kubernetes Cluster Required:** Created During This Module

Learners can choose a Kubernetes installation method based on their hardware, cloud infrastructure, budget and learning requirements.

# 🟢 Option A — Single-Node Kubernetes Cluster

**Recommended for beginners and learners with limited infrastructure.**

- **Lab 2001:** Install Single-Node K3s Kubernetes on Ubuntu
- **Lab 2002:** Install Single-Node Minikube Kubernetes
- **Lab 2003:** Install Single-Node kind Kubernetes

**Recommended Environment:**

- Local Ubuntu machine
- Ubuntu virtual machine
- AWS EC2 Ubuntu instance with suitable resources

**Use Cases:**

- Kubernetes Fundamentals
- kubectl and YAML
- Pods and Deployments
- Services and Networking
- ConfigMaps and Secrets
- Basic Storage and Security
- Application Troubleshooting

# 🔵 Option B — Multi-Node Kubernetes Cluster

**Recommended for cloud engineering and advanced Kubernetes learners.**

- **Lab 2004:** Install Multi-Node Kubernetes Using kOps on AWS
- **Lab 2005:** Create Multi-Node Amazon EKS Cluster
- **Lab 2006:** Create Multi-Node Azure AKS Cluster

**Recommended Environment:**

- AWS Cloud
- Microsoft Azure
- Multiple Kubernetes worker nodes

**Use Cases:**

- Kubernetes Cluster Administration
- Multi-Node Scheduling
- Node Affinity and Anti-Affinity
- Taints and Tolerations
- Autoscaling
- High Availability
- Advanced Networking
- Production-Oriented Operations

**Cloud Cost Warning:** AWS and Azure resources may incur charges. Follow the cleanup instructions after completing each lab.

# 🟣 Option C — Kubernetes Cluster Installation Using kubeadm

**Recommended for advanced Kubernetes administration.**

- **Lab 2007:** Install Kubernetes Cluster Using kubeadm on Ubuntu

**Recommended Environment:**

- Multiple Ubuntu virtual machines
- AWS EC2 Ubuntu instances
- Dedicated Linux infrastructure

**Use Cases:**

- Kubernetes Control-Plane Installation
- Worker-Node Configuration
- containerd Configuration
- Kubernetes Networking and CNI
- kubelet and kubeadm Administration
- Cluster Upgrades
- etcd Administration
- Control-Plane Troubleshooting

# ✅ Mandatory Cluster Readiness Lab

- **Lab 2008:** Verify Kubernetes Cluster and Lab Readiness

**Mandatory Verification Requirements:**

- Kubernetes cluster is accessible.
- kubectl is installed and configured.
- Kubernetes nodes are Ready.
- Essential system Pods are healthy.
- A sample application can be deployed.
- Application status can be verified.
- Test resources can be cleaned up.

**Important:** Learners must complete one appropriate cluster installation path and pass Lab 2008 before proceeding to Module 03.

**Training Recommendation:** Single-node learners may use K3s, Minikube or kind. Multi-node cloud setups and kubeadm administration are available as alternative or advanced practical exercises.

---

# 📁 Module 03 — kubectl, YAML & Kubernetes Resource Management

**Level:** Beginner  
**Total Labs:** 5  
**Kubernetes Cluster Required:** Yes

- **Lab 3001:** Master Essential kubectl Commands
- **Lab 3002:** Create and Manage Kubernetes YAML Manifests
- **Lab 3003:** Manage Namespaces, Labels and Annotations
- **Lab 3004:** Inspect, Edit, Patch and Delete Kubernetes Resources
- **Lab 3005:** Manage Kubernetes Contexts and kubeconfig

**Learning Outcome:** Manage Kubernetes resources confidently using kubectl and YAML manifests.

**Recommended Environment:** Any suitable Kubernetes cluster with kubectl access.

**Prerequisite:** Complete Lab 2008 before executing Module 03.

---

# 📁 Module 04 — Pods, Containers & Workload Management

**Level:** Beginner to Intermediate  
**Total Labs:** 9

- **Lab 4001:** Create and Manage Kubernetes Pods
- **Lab 4002:** Configure Multi-Container Pods and Init Containers
- **Lab 4003:** Create and Manage ReplicaSets and Deployments
- **Lab 4004:** Scale Kubernetes Deployments
- **Lab 4005:** Perform Rolling Updates and Rollbacks
- **Lab 4006:** Configure Liveness, Readiness and Startup Probes
- **Lab 4007:** Deploy DaemonSets and StatefulSets
- **Lab 4008:** Create and Schedule Kubernetes Jobs and CronJobs
- **Lab 4009:** Configure Kubernetes Sidecar Containers and Lifecycle Hooks

**Learning Outcome:** Deploy, manage, scale, update and troubleshoot Kubernetes application workloads.

**Additional Practical Coverage:**

- Kubernetes sidecar container configuration
- Container startup and termination behavior
- Container lifecycle hooks
- Application initialization and graceful shutdown

**Recommended Environment:** Single-node or multi-node Kubernetes cluster.

---

# 📁 Module 05 — Configuration, Secrets & Resource Management

**Level:** Intermediate  
**Total Labs:** 4

- **Lab 5001:** Configure Applications Using ConfigMaps
- **Lab 5002:** Manage Kubernetes Secrets and Private Registry Access
- **Lab 5003:** Configure CPU and Memory Requests and Limits
- **Lab 5004:** Enforce ResourceQuota and LimitRange

**Learning Outcome:** Manage application configuration, sensitive information and resource allocation.

**Recommended Environment:** Single-node or multi-node Kubernetes cluster.

---

# 📁 Module 06 — Kubernetes Networking & Services

**Level:** Intermediate  
**Total Labs:** 7

- **Lab 6001:** Explore Pod-to-Pod Network Communication
- **Lab 6002:** Expose Applications Using ClusterIP and NodePort
- **Lab 6003:** Configure LoadBalancer and Headless Services
- **Lab 6004:** Test Kubernetes DNS and Service Discovery
- **Lab 6005:** Configure Ingress and Gateway API Routing
- **Lab 6006:** Implement Kubernetes NetworkPolicies
- **Lab 6007:** Troubleshoot Kubernetes CNI and NetworkPolicy Connectivity

**Learning Outcome:** Configure Kubernetes networking, application connectivity, routing and network security.

**Additional Practical Coverage:**

- CNI networking diagnostics
- Pod communication failures
- NetworkPolicy traffic isolation failures
- DNS and connectivity investigation
- Network troubleshooting using kubectl and Linux utilities

**Environment Note:**

- LoadBalancer, Ingress and Gateway API labs may require additional components.
- NetworkPolicy enforcement requires a compatible CNI implementation.
- CNI troubleshooting procedures can differ between K3s, Minikube, kind, EKS, AKS and kubeadm.
- Advanced CNI diagnostics should use an isolated test cluster.

---

# 📁 Module 07 — Kubernetes Storage & Persistence

**Level:** Intermediate  
**Total Labs:** 6

- **Lab 7001:** Configure emptyDir and hostPath Volumes
- **Lab 7002:** Create PersistentVolumes and PersistentVolumeClaims
- **Lab 7003:** Configure StorageClasses and Dynamic Provisioning
- **Lab 7004:** Deploy StatefulSets with Persistent Storage
- **Lab 7005:** Verify Data Persistence and Troubleshoot Storage
- **Lab 7006:** Configure Kubernetes Volume Expansion and VolumeSnapshots

**Learning Outcome:** Configure persistent storage, storage provisioning and stateful Kubernetes applications.

**Additional Practical Coverage:**

- PersistentVolumeClaim expansion
- StorageClass volume expansion configuration
- VolumeSnapshot resources
- VolumeSnapshotClasses
- Snapshot creation and restore validation

**Environment Note:**

- Dynamic storage provisioning depends on an available StorageClass.
- Volume expansion requires support from the storage provisioner and driver.
- VolumeSnapshots require compatible CSI snapshot components.
- Local Kubernetes environments may require additional storage drivers for advanced exercises.

---

# 📁 Module 08 — Kubernetes Security, RBAC & Access Control

**Level:** Intermediate to Advanced  
**Total Labs:** 7

- **Lab 8001:** Create and Manage Kubernetes ServiceAccounts
- **Lab 8002:** Configure Roles, RoleBindings and RBAC
- **Lab 8003:** Implement ClusterRoles and ClusterRoleBindings
- **Lab 8004:** Secure Containers Using SecurityContext
- **Lab 8005:** Enforce Pod Security Admission and Least Privilege
- **Lab 8006:** Configure Kubernetes Secrets Encryption at Rest
- **Lab 8007:** Configure Kubernetes Audit Logging

**Learning Outcome:** Secure Kubernetes workloads, implement role-based access and enforce security policies.

**Additional Practical Coverage:**

- Kubernetes encryption configuration
- Encrypting Kubernetes Secrets at rest
- Encryption verification
- Kubernetes API audit logging
- Audit policy configuration
- Investigating Kubernetes API audit records

**Environment Note:**

- Labs 8001–8005 can run on suitable Kubernetes clusters with the required permissions.
- Labs 8006–8007 require appropriate control-plane administrative access.
- A dedicated self-managed kubeadm cluster is recommended for these advanced security exercises.
- Managed EKS and AKS use provider-specific encryption and audit logging mechanisms.
- Never expose actual credentials, private keys or sensitive audit information in GitHub.

---

# 📁 Module 09 — Kubernetes Scheduling, Autoscaling & High Availability

**Level:** Advanced  
**Total Labs:** 8

- **Lab 9001:** Schedule Pods Using nodeSelector and Node Affinity
- **Lab 9002:** Configure Pod Affinity and Anti-Affinity
- **Lab 9003:** Configure Node Taints and Pod Tolerations
- **Lab 9004:** Configure Horizontal Pod Autoscaler
- **Lab 9005:** Implement PodDisruptionBudgets and Topology Spread
- **Lab 9006:** Perform Node Cordon, Drain and Uncordon
- **Lab 9007:** Configure Vertical Pod Autoscaler
- **Lab 9008:** Explore Kubernetes Node Autoscaling with Karpenter or Cluster Autoscaler

**Learning Outcome:** Control Pod scheduling, implement autoscaling and improve application availability.

**Additional Practical Coverage:**

- Vertical Pod Autoscaler architecture
- Workload resource recommendations
- Vertical resource adjustment
- Kubernetes node autoscaling architecture
- Karpenter and Cluster Autoscaler fundamentals
- Cloud-based node provisioning and scaling behavior

**Environment Note:**

- Some labs require multiple schedulable Kubernetes nodes.
- Single-node learners may create an additional multi-node kind cluster where hardware resources permit.
- Horizontal Pod Autoscaler exercises require a suitable metrics source.
- Vertical Pod Autoscaler requires installation of the appropriate VPA components.
- Karpenter and Cluster Autoscaler exercises require supported infrastructure integrations.
- Lab 9008 is an advanced optional exercise and may incur cloud infrastructure charges.

---

# 📁 Module 10 — Helm, Kustomize, CRDs & GitOps

**Level:** Advanced  
**Total Labs:** 6

- **Lab 10001:** Install and Manage Applications Using Helm
- **Lab 10002:** Create and Customize Helm Charts
- **Lab 10003:** Manage Kubernetes Manifests Using Kustomize
- **Lab 10004:** Create and Explore Kubernetes CustomResourceDefinitions
- **Lab 10005:** Implement GitOps Deployment Using Argo CD
- **Lab 10006:** Implement Blue-Green and Canary Deployment Strategies

**Learning Outcome:** Package, customize and automate Kubernetes application deployments using modern delivery practices.

**Additional Practical Coverage:**

- Blue-green deployment concepts
- Deploying multiple application versions
- Traffic switching between application versions
- Canary rollout concepts
- Progressive application delivery
- Deployment verification and rollback

**Environment Note:**

- Basic Helm and Kustomize labs can run on single-node clusters.
- GitOps labs require installation of a supported GitOps controller.
- Advanced progressive delivery may require an Ingress controller, Gateway implementation, service mesh or rollout controller.
- Use an isolated lab namespace and test workloads for deployment strategy exercises.

---

# 📁 Module 11 — Kubernetes Cluster Administration & Lifecycle Management

**Level:** Advanced Administration  
**Total Labs:** 5

- **Lab 11001:** Administer kubeadm Control Plane and Worker Nodes
- **Lab 11002:** Manage Kubernetes Certificates and kubeconfig
- **Lab 11003:** Perform Kubernetes Cluster and Node Upgrades
- **Lab 11004:** Perform etcd Backup and Restore
- **Lab 11005:** Troubleshoot kubelet and Node Services

**Learning Outcome:** Perform Kubernetes cluster maintenance, upgrades, administrative operations and recovery.

**Environment Note:**

- These labs primarily use self-managed Kubernetes clusters.
- Ubuntu VMs or AWS EC2 Ubuntu instances are recommended.
- Managed EKS and AKS control planes do not expose equivalent kubeadm or etcd administration capabilities.
- Perform disruptive operations only on dedicated lab clusters.
- Take appropriate backups before performing upgrades or recovery exercises.
- Confirm Kubernetes component version compatibility before starting cluster upgrades.

---

# 📁 Module 12 — Kubernetes Observability, Monitoring & Production Troubleshooting

**Level:** Advanced Production Troubleshooting  
**Total Labs:** 15

- **Lab 12001:** Monitor Kubernetes Resources Using kubectl top
- **Lab 12002:** Monitor Kubernetes Using Prometheus and Grafana
- **Lab 12003:** Troubleshoot Kubernetes Pod Stuck in Pending
- **Lab 12004:** Troubleshoot Kubernetes Pod CrashLoopBackOff
- **Lab 12005:** Troubleshoot ImagePullBackOff and ErrImagePull
- **Lab 12006:** Troubleshoot Pod Stuck in ContainerCreating
- **Lab 12007:** Troubleshoot OOMKilled and Memory Limit Issues
- **Lab 12008:** Troubleshoot Pod Running but Application Not Accessible
- **Lab 12009:** Troubleshoot Deployment Rollout Failures
- **Lab 12010:** Troubleshoot Kubernetes Service and DNS Issues
- **Lab 12011:** Troubleshoot PersistentVolume and PVC Issues
- **Lab 12012:** Troubleshoot Node NotReady and Resource Pressure
- **Lab 12013:** Troubleshoot Kubernetes Ingress and Gateway Routing Failures
- **Lab 12014:** Troubleshoot Kubernetes RBAC Forbidden Errors
- **Lab 12015:** Perform Kubernetes Incident Investigation and Root Cause Analysis

**Learning Outcome:** Investigate Kubernetes incidents using logs, metrics, events and diagnostic commands.

**Additional Practical Coverage:**

- Ingress and Gateway route troubleshooting
- Backend Service and endpoint validation
- HTTP routing and TLS investigation
- Kubernetes RBAC authorization failures
- Forbidden API responses
- Investigating Roles and RoleBindings
- Structured Kubernetes incident investigation
- Root-cause analysis documentation
- Incident resolution and recovery verification

# 🔎 Production Troubleshooting Methodology

Every troubleshooting lab follows a structured investigation process.

1. Identify the problem.
2. Inspect Kubernetes resource status.
3. Analyze events and logs.
4. Determine the root cause.
5. Apply the corrective action.
6. Verify application or cluster recovery.
7. Document the findings.
8. Clean up test resources.

**Troubleshooting Tools:**

- kubectl get
- kubectl describe
- kubectl logs
- kubectl exec
- kubectl top
- kubectl auth can-i
- kubectl rollout
- Kubernetes Events
- Linux networking utilities
- kubelet and systemd logs
- Prometheus and Grafana, where configured

**Environment Note:**

- Most workload troubleshooting labs can run on single-node or multi-node clusters.
- Node-level diagnostics require appropriate infrastructure access.
- Ingress and Gateway troubleshooting require the relevant controllers.
- RBAC troubleshooting requires suitable test identities and permissions.
- Advanced cluster failure simulations should use dedicated training infrastructure.

**Important:** Troubleshooting scenarios must use dedicated lab environments and should not intentionally disrupt shared or production clusters.

---

# 📁 Module 13 — Kubernetes Production Operations & Capstone Project

**Level:** Production Engineering  
**Total Labs:** 3

- **Lab 13001:** Deploy Production-Oriented Multi-Tier Application
- **Lab 13002:** Validate Security, Scaling, Monitoring and Recovery
- **Lab 13003:** Kubernetes Production Capstone Assessment

**Learning Outcome:** Integrate the skills acquired throughout the training program into a production-oriented Kubernetes project.

---

# 🏆 Final Capstone Project

**Project:** Deploy and Operate a Multi-Tier Application on Kubernetes

**Project Requirements:**

- Deploy frontend and backend applications.
- Configure internal Kubernetes Services.
- Configure external application access.
- Implement ConfigMaps and Secrets.
- Deploy persistent database storage.
- Configure application health probes.
- Configure CPU and memory resources.
- Implement ServiceAccounts and RBAC.
- Apply appropriate container security controls.
- Implement NetworkPolicies.
- Configure autoscaling where supported.
- Configure application monitoring.
- Perform application updates and rollbacks.
- Validate backup and recovery where supported.
- Investigate and resolve simulated failures.
- Prepare technical documentation.
- Demonstrate the final solution.

**Final Assessment:** Learners must demonstrate their ability to deploy, configure, secure, manage and troubleshoot Kubernetes applications independently.

---

# 🆕 Additional Advanced Hands-On Labs — Version 2.3

Version 2.3 introduced 11 additional practical exercises, retained in Version 2.4, to strengthen alignment between the course curriculum and hands-on training.

| Lab ID | Additional Hands-On Lab | Module |
|---|---|---|
| 4009 | Configure Kubernetes Sidecar Containers and Lifecycle Hooks | 04 |
| 6007 | Troubleshoot Kubernetes CNI and NetworkPolicy Connectivity | 06 |
| 7006 | Configure Kubernetes Volume Expansion and VolumeSnapshots | 07 |
| 8006 | Configure Kubernetes Secrets Encryption at Rest | 08 |
| 8007 | Configure Kubernetes Audit Logging | 08 |
| 9007 | Configure Vertical Pod Autoscaler | 09 |
| 9008 | Explore Kubernetes Node Autoscaling with Karpenter or Cluster Autoscaler | 09 |
| 10006 | Implement Blue-Green and Canary Deployment Strategies | 10 |
| 12013 | Troubleshoot Kubernetes Ingress and Gateway Routing Failures | 12 |
| 12014 | Troubleshoot Kubernetes RBAC Forbidden Errors | 12 |
| 12015 | Perform Kubernetes Incident Investigation and Root Cause Analysis | 12 |

**Total Additional Labs:** 11

**Advanced Lab Guidance:**

- Some advanced labs are optional.
- Infrastructure-specific labs require suitable environments.
- Cloud autoscaling labs may incur additional charges.
- Administrative security labs require privileged access.
- Learners should complete prerequisite labs before attempting advanced scenarios.

---

# 📊 Lab Distribution Summary

| Module | Topic | Lab IDs | Labs |
|---|---|---|---:|
| 00 | Kubernetes Orientation & Prerequisites | 0000–0001 | 2 |
| 01 | Kubernetes Fundamentals & Architecture | 1001–1002 | 2 |
| 02 | Kubernetes Cluster Installation | 2001–2008 | 8 |
| 03 | kubectl, YAML & Resource Management | 3001–3005 | 5 |
| 04 | Pods, Containers & Workload Management | 4001–4009 | 9 |
| 05 | Configuration, Secrets & Resources | 5001–5004 | 4 |
| 06 | Kubernetes Networking & Services | 6001–6007 | 7 |
| 07 | Kubernetes Storage & Persistence | 7001–7006 | 6 |
| 08 | Kubernetes Security & RBAC | 8001–8007 | 7 |
| 09 | Scheduling, Autoscaling & High Availability | 9001–9008 | 8 |
| 10 | Helm, Kustomize, CRDs & GitOps | 10001–10006 | 6 |
| 11 | Cluster Administration & Lifecycle | 11001–11005 | 5 |
| 12 | Observability & Production Troubleshooting | 12001–12015 | 15 |
| 13 | Production Operations & Capstone | 13001–13003 | 3 |
| **Total** | **14 Modules** | | **87** |

**Note:** The catalogue includes optional cluster installation alternatives and environment-specific advanced labs. Not every learner must complete all 87 labs.

---

# 🎓 Learner Lab Execution Guidelines

| Learning Stage | Requirements |
|---|---|
| Module 00 | Complete Linux and workstation preparation; no Kubernetes cluster needed |
| Module 01 | Complete architecture and conceptual exercises; no Kubernetes cluster needed |
| Module 02 | Choose one cluster installation and pass Lab 2008 |
| Modules 03–05 | Practice kubectl, YAML, workloads and configuration |
| Modules 06–07 | Complete networking and storage exercises |
| Module 08 | Complete Kubernetes security and RBAC labs |
| Module 09 | Use multiple nodes for node-dependent exercises |
| Module 10 | Complete Helm, Kustomize and GitOps labs |
| Module 11 | Use a suitable administrative Kubernetes environment |
| Module 12 | Complete monitoring and troubleshooting scenarios |
| Module 13 | Complete the final production-oriented capstone |

---

# ✅ Recommended Lab Execution Order

Learners should follow the module sequence to ensure that each new topic builds on previously acquired skills.

**Foundation Stage:**

1. Complete Module 00 Linux and workstation prerequisites.
2. Understand Kubernetes architecture in Module 01.
3. Choose a cluster installation option in Module 02.
4. Complete mandatory Lab 2008.

**Application Engineering Stage:**

1. Learn kubectl and YAML in Module 03.
2. Deploy Kubernetes workloads in Module 04.
3. Manage application configuration in Module 05.
4. Configure Kubernetes networking in Module 06.
5. Implement persistent storage in Module 07.

**Advanced Engineering Stage:**

1. Implement Kubernetes security in Module 08.
2. Practice scheduling and autoscaling in Module 09.
3. Deploy applications using Helm and GitOps in Module 10.

**Production Engineering Stage:**

1. Perform cluster administration in Module 11.
2. Investigate production troubleshooting scenarios in Module 12.
3. Complete the capstone project in Module 13.

---

# 🧪 Practical Skills Assessment

Learners should demonstrate practical competency at the end of each major learning stage.

| Assessment | Modules | Expected Skills |
|---|---|---|
| Linux Readiness | 00 | Prepare and validate a Linux environment |
| Architecture Understanding | 01 | Explain Kubernetes architecture and reconciliation |
| Cluster Readiness | 02 | Install and verify a Kubernetes cluster |
| Workload Management | 03–05 | Deploy, configure and update applications |
| Networking and Storage | 06–07 | Expose applications and configure persistence |
| Security and Reliability | 08–09 | Secure, schedule and scale workloads |
| Application Delivery | 10 | Package and automate deployments |
| Cluster Administration | 11 | Perform administrative and recovery operations |
| Production Troubleshooting | 12 | Investigate and resolve Kubernetes failures |
| Final Capstone | 13 | Deliver and demonstrate an integrated Kubernetes solution |

---

# ✅ Standard Lab Documentation Format

Every Vyomanant Academy lab follows a consistent, beginner-friendly structure.

**Required Sections:**

1. 🌍 Lab Overview
2. 🎯 Learning Objectives
3. 🧰 Prerequisites
4. 🖥️ Lab Environment
5. 🏗️ Lab Architecture (if applicable)
6. 🚀 Step-by-Step Instructions
7. 🔎 Verification
8. 🛠️ Troubleshooting (where applicable)
9. 🧹 Cleanup
10. 🧠 Key Takeaways
11. 📚 Official Documentation

**Lab Authoring Standards:**

- Use Ubuntu-based environments wherever applicable.
- Prefer AWS EC2 Ubuntu or local Ubuntu machines.
- Use MobaXterm for remote SSH access from Windows.
- Use `vi` for interactive file editing.
- Provide commands in correct execution order.
- Explain important commands clearly.
- Include expected verification results.
- Identify environment-specific prerequisites.
- Use stable, officially supported approaches.
- Include cleanup instructions for billable cloud resources.
- Keep examples beginner-friendly and production-relevant.
- State whether a lab supports single-node or multi-node clusters.
- Identify additional controllers, storage drivers or metrics components when required.
- Clearly label optional and advanced infrastructure-dependent exercises.
- Never expose real credentials or sensitive configuration information in documentation.
- Do not require a Kubernetes cluster for Modules 00 or 01.
- Begin live cluster execution during Module 02.
- Require completion of Lab 2008 before starting Module 03.

---

# 📂 Recommended GitHub Repository Structure

**Repository Name:** Vyomanant-Academy-Kubernetes

**Root Documentation:**

- README.md
- COURSE-TOC.md
- LAB-CATALOGUE.md
- PREREQUISITES.md

**Module Directories:**

- Module-00-Orientation-and-Prerequisites
- Module-01-Kubernetes-Fundamentals-and-Architecture
- Module-02-Kubernetes-Cluster-Setup
- Module-03-Kubectl-YAML-and-Resource-Management
- Module-04-Pods-and-Workloads
- Module-05-Configuration-and-Resources
- Module-06-Networking-and-Services
- Module-07-Storage-and-Persistence
- Module-08-Security-and-RBAC
- Module-09-Scheduling-Scaling-and-HA
- Module-10-Helm-Kustomize-and-GitOps
- Module-11-Cluster-Administration
- Module-12-Observability-and-Troubleshooting
- Module-13-Production-Capstone

**Repository Organization Rules:**

- Each module must have its own folder.
- Each module should contain a README.md file.
- Store individual lab guides using their assigned lab IDs.
- Maintain the same lab names in the catalogue and lab filenames.
- Store supporting YAML manifests alongside their respective labs or in dedicated lab-specific subdirectories.
- Do not store passwords, private keys, kubeconfig credentials or cloud secrets in GitHub.
- Keep screenshots and architecture diagrams in clearly named asset folders.
- Update LAB-CATALOGUE.md whenever a new lab is added.
- Preserve existing lab numbering when introducing new exercises.
- Maintain separate documentation for environment-specific setup requirements.

---

# 📚 Official Kubernetes Documentation

**Core Kubernetes:**

- [Kubernetes Official Documentation](https://kubernetes.io/docs/)
- [Kubernetes Concepts](https://kubernetes.io/docs/concepts/)
- [Kubernetes Tutorials](https://kubernetes.io/docs/tutorials/)
- [Kubernetes Installation](https://kubernetes.io/docs/setup/)
- [kubectl Reference](https://kubernetes.io/docs/reference/kubectl/)
- [Kubernetes Troubleshooting](https://kubernetes.io/docs/tasks/debug/)

**Kubernetes Cluster Setup:**

- [K3s Documentation](https://docs.k3s.io/)
- [Minikube Documentation](https://minikube.sigs.k8s.io/docs/)
- [kind Documentation](https://kind.sigs.k8s.io/)
- [kOps Documentation](https://kops.sigs.k8s.io/)
- [kubeadm Documentation](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/)
- [Amazon EKS Documentation](https://docs.aws.amazon.com/eks/)
- [Azure AKS Documentation](https://learn.microsoft.com/en-us/azure/aks/)

**Kubernetes Security:**

- [Kubernetes RBAC](https://kubernetes.io/docs/reference/access-authn-authz/rbac/)
- [Pod Security Standards](https://kubernetes.io/docs/concepts/security/pod-security-standards/)
- [Encrypting Secret Data at Rest](https://kubernetes.io/docs/tasks/administer-cluster/encrypt-data/)
- [Kubernetes Auditing](https://kubernetes.io/docs/tasks/debug/debug-cluster/audit/)

**Kubernetes Storage:**

- [Kubernetes Persistent Volumes](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)
- [Kubernetes Storage Classes](https://kubernetes.io/docs/concepts/storage/storage-classes/)
- [Kubernetes Volume Snapshots](https://kubernetes.io/docs/concepts/storage/volume-snapshots/)

**Kubernetes Scheduling and Autoscaling:**

- [Kubernetes Scheduling](https://kubernetes.io/docs/concepts/scheduling-eviction/)
- [Horizontal Pod Autoscaling](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/)
- [Kubernetes Autoscaling](https://kubernetes.io/docs/concepts/workloads/autoscaling/)
- [Karpenter Documentation](https://karpenter.sh/)

**Application Delivery and GitOps:**

- [Helm Documentation](https://helm.sh/docs/)
- [Kustomize Documentation](https://kubectl.docs.kubernetes.io/)
- [Gateway API Documentation](https://gateway-api.sigs.k8s.io/)
- [Argo CD Documentation](https://argo-cd.readthedocs.io/)

**Monitoring and Observability:**

- [Prometheus Documentation](https://prometheus.io/docs/)
- [Grafana Documentation](https://grafana.com/docs/)

---

# 🚀 Begin Your Kubernetes Learning Journey

Start with **Lab 0000 — Verify Linux, Container and Networking Prerequisites**.

Follow the modules in the recommended learning order:

1. Prepare the Ubuntu lab environment in Module 00.
2. Understand Kubernetes architecture through conceptual exercises in Module 01.
3. Install a Kubernetes cluster in Module 02.
4. Complete Lab 2008 — Verify Kubernetes Cluster and Lab Readiness.
5. Begin regular Kubernetes hands-on operations in Module 03.
6. Progress through workloads, networking, storage, security and advanced platform engineering.
7. Complete production troubleshooting and the capstone project.

**Recommended Learning Approach:**

- Complete the prerequisite labs.
- Understand Kubernetes architecture before installation.
- Establish a working Kubernetes lab environment.
- Practice each module's hands-on exercises.
- Verify the expected results.
- Document troubleshooting findings.
- Complete optional advanced labs when suitable infrastructure is available.
- Finish the production-oriented capstone project.

---

**Vyomanant Academy**  
*Kubernetes Administration, Engineering & Production Troubleshooting*  
**Master Hands-On Lab Catalogue | Version 2.4**
