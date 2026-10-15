
# Kubernetes Administration, Engineering & Production Troubleshooting

**Master Hands-On Lab Catalogue | Version 2.2**

**Learning Path:** Beginner → Intermediate → Advanced → Production Engineering  
**Lab Series:** Module-Based Numbering (0000–13003)  
**Total Labs:** 76  
**Total Modules:** 14  
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

---

# 🔢 Lab Numbering Convention

Every lab uses a unique identifier based on its module number and sequence.

| Module | Lab Numbering | Example |
|---|---|---|
| Module 00 | 0000–0001 | 0000-Verify-Linux-Prerequisites.md |
| Module 01 | 1001–1002 | 1001-Explore-Kubernetes-Architecture.md |
| Module 02 | 2001–2008 | 2001-Install-Single-Node-K3s-Kubernetes-on-Ubuntu.md |
| Module 03 | 3001–3005 | 3001-Master-Essential-Kubectl-Commands.md |
| Module 04 | 4001–4008 | 4001-Create-and-Manage-Kubernetes-Pods.md |
| Module 05 | 5001–5004 | 5001-Configure-Applications-Using-ConfigMaps.md |
| Module 06 | 6001–6006 | 6001-Explore-Pod-to-Pod-Network-Communication.md |
| Module 07 | 7001–7005 | 7001-Configure-EmptyDir-and-HostPath-Volumes.md |
| Module 08 | 8001–8005 | 8001-Create-and-Manage-Kubernetes-ServiceAccounts.md |
| Module 09 | 9001–9006 | 9001-Schedule-Pods-Using-NodeSelector-and-Node-Affinity.md |
| Module 10 | 10001–10005 | 10001-Install-and-Manage-Applications-Using-Helm.md |
| Module 11 | 11001–11005 | 11001-Administer-Kubeadm-Control-Plane-and-Worker-Nodes.md |
| Module 12 | 12001–12012 | 12001-Monitor-Kubernetes-Resources-Using-Kubectl-Top.md |
| Module 13 | 13001–13003 | 13001-Deploy-Production-Oriented-MultiTier-Application.md |

**Naming Standards:**

- Every lab receives a unique module-based lab ID.
- Every lab is maintained as an individual Markdown file.
- Lab filenames begin with the assigned lab ID.
- Use descriptive names separated by hyphens.
- Keep labs organized inside their respective module directories.
- Preserve existing lab IDs when adding new labs.

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
- AWS EC2 Ubuntu and local Ubuntu are the primary infrastructure environments.
- Managed Kubernetes services such as EKS and AKS may require provider-specific operating systems and infrastructure.
- Use `vi` for all interactive file editing.
- Use MobaXterm for remote SSH access from Windows systems.
- Kubernetes version and infrastructure requirements will be specified in individual labs.
- Always clean up unused AWS and Azure resources to prevent unnecessary charges.

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

- **Lab 0000:** Verify Linux, Container and Networking Prerequisites
- **Lab 0001:** Prepare Kubernetes Lab Workstation

**Learning Outcome:** Prepare a local Ubuntu or AWS EC2 Ubuntu environment with the essential tools and prerequisites for Kubernetes training.

---

# 📁 Module 01 — Kubernetes Fundamentals & Architecture

**Level:** Beginner  
**Total Labs:** 2

- **Lab 1001:** Explore Kubernetes Architecture and Components
- **Lab 1002:** Understand Kubernetes API and Reconciliation

**Learning Outcome:** Understand Kubernetes cluster architecture, control-plane components, worker nodes, API interactions and reconciliation.

**Note:** Labs requiring a running cluster can initially use an instructor-provided environment or be executed after Module 02.

---

# 📁 Module 02 — Kubernetes Cluster Installation & Lab Environment Setup

**Level:** Beginner to Advanced  
**Total Labs:** 8

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

- **Lab 3001:** Master Essential kubectl Commands
- **Lab 3002:** Create and Manage Kubernetes YAML Manifests
- **Lab 3003:** Manage Namespaces, Labels and Annotations
- **Lab 3004:** Inspect, Edit, Patch and Delete Kubernetes Resources
- **Lab 3005:** Manage Kubernetes Contexts and kubeconfig

**Learning Outcome:** Manage Kubernetes resources confidently using kubectl and YAML manifests.

---

# 📁 Module 04 — Pods, Containers & Workload Management

**Level:** Beginner to Intermediate  
**Total Labs:** 8

- **Lab 4001:** Create and Manage Kubernetes Pods
- **Lab 4002:** Configure Multi-Container Pods and Init Containers
- **Lab 4003:** Create and Manage ReplicaSets and Deployments
- **Lab 4004:** Scale Kubernetes Deployments
- **Lab 4005:** Perform Rolling Updates and Rollbacks
- **Lab 4006:** Configure Liveness, Readiness and Startup Probes
- **Lab 4007:** Deploy DaemonSets and StatefulSets
- **Lab 4008:** Create and Schedule Kubernetes Jobs and CronJobs

**Learning Outcome:** Deploy, manage, scale, update and troubleshoot Kubernetes application workloads.

---

# 📁 Module 05 — Configuration, Secrets & Resource Management

**Level:** Intermediate  
**Total Labs:** 4

- **Lab 5001:** Configure Applications Using ConfigMaps
- **Lab 5002:** Manage Kubernetes Secrets and Private Registry Access
- **Lab 5003:** Configure CPU and Memory Requests and Limits
- **Lab 5004:** Enforce ResourceQuota and LimitRange

**Learning Outcome:** Manage application configuration, sensitive information and resource allocation.

---

# 📁 Module 06 — Kubernetes Networking & Services

**Level:** Intermediate  
**Total Labs:** 6

- **Lab 6001:** Explore Pod-to-Pod Network Communication
- **Lab 6002:** Expose Applications Using ClusterIP and NodePort
- **Lab 6003:** Configure LoadBalancer and Headless Services
- **Lab 6004:** Test Kubernetes DNS and Service Discovery
- **Lab 6005:** Configure Ingress and Gateway API Routing
- **Lab 6006:** Implement Kubernetes NetworkPolicies

**Learning Outcome:** Configure Kubernetes networking, application connectivity, routing and network security.

**Environment Note:** LoadBalancer, Ingress, Gateway API and NetworkPolicy labs may require additional controllers or compatible networking components.

---

# 📁 Module 07 — Kubernetes Storage & Persistence

**Level:** Intermediate  
**Total Labs:** 5

- **Lab 7001:** Configure emptyDir and hostPath Volumes
- **Lab 7002:** Create PersistentVolumes and PersistentVolumeClaims
- **Lab 7003:** Configure StorageClasses and Dynamic Provisioning
- **Lab 7004:** Deploy StatefulSets with Persistent Storage
- **Lab 7005:** Verify Data Persistence and Troubleshoot Storage

**Learning Outcome:** Configure persistent storage, storage provisioning and stateful Kubernetes applications.

**Environment Note:** Dynamic provisioning and snapshot capabilities depend on the available StorageClass, CSI driver and cluster configuration.

---

# 📁 Module 08 — Kubernetes Security, RBAC & Access Control

**Level:** Intermediate to Advanced  
**Total Labs:** 5

- **Lab 8001:** Create and Manage Kubernetes ServiceAccounts
- **Lab 8002:** Configure Roles, RoleBindings and RBAC
- **Lab 8003:** Implement ClusterRoles and ClusterRoleBindings
- **Lab 8004:** Secure Containers Using SecurityContext
- **Lab 8005:** Enforce Pod Security Admission and Least Privilege

**Learning Outcome:** Secure Kubernetes workloads, implement role-based access and enforce security policies.

---

# 📁 Module 09 — Kubernetes Scheduling, Autoscaling & High Availability

**Level:** Advanced  
**Total Labs:** 6

- **Lab 9001:** Schedule Pods Using nodeSelector and Node Affinity
- **Lab 9002:** Configure Pod Affinity and Anti-Affinity
- **Lab 9003:** Configure Node Taints and Pod Tolerations
- **Lab 9004:** Configure Horizontal Pod Autoscaler
- **Lab 9005:** Implement PodDisruptionBudgets and Topology Spread
- **Lab 9006:** Perform Node Cordon, Drain and Uncordon

**Learning Outcome:** Control Pod scheduling, implement autoscaling and improve application availability.

**Environment Note:**

- Some labs require multiple schedulable Kubernetes nodes.
- Single-node learners may create an additional multi-node kind cluster where hardware resources permit.
- Horizontal Pod Autoscaler exercises require a suitable metrics source.

---

# 📁 Module 10 — Helm, Kustomize, CRDs & GitOps

**Level:** Advanced  
**Total Labs:** 5

- **Lab 10001:** Install and Manage Applications Using Helm
- **Lab 10002:** Create and Customize Helm Charts
- **Lab 10003:** Manage Kubernetes Manifests Using Kustomize
- **Lab 10004:** Create and Explore Kubernetes CustomResourceDefinitions
- **Lab 10005:** Implement GitOps Deployment Using Argo CD

**Learning Outcome:** Package, customize and automate Kubernetes application deployments using modern delivery practices.

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

---

# 📁 Module 12 — Kubernetes Observability, Monitoring & Production Troubleshooting

**Level:** Advanced Production Troubleshooting  
**Total Labs:** 12

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

**Learning Outcome:** Investigate Kubernetes incidents using logs, metrics, events and diagnostic commands.

**Troubleshooting Methodology:**

1. Identify the problem.
2. Inspect Kubernetes resource status.
3. Analyze events and logs.
4. Determine the root cause.
5. Apply the corrective action.
6. Verify application or cluster recovery.
7. Document the findings.
8. Clean up test resources.

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

# 📊 Lab Distribution Summary

| Module | Topic | Lab IDs | Labs |
|---|---|---|---:|
| 00 | Kubernetes Orientation & Prerequisites | 0000–0001 | 2 |
| 01 | Kubernetes Fundamentals & Architecture | 1001–1002 | 2 |
| 02 | Kubernetes Cluster Installation | 2001–2008 | 8 |
| 03 | kubectl, YAML & Resource Management | 3001–3005 | 5 |
| 04 | Pods, Containers & Workload Management | 4001–4008 | 8 |
| 05 | Configuration, Secrets & Resources | 5001–5004 | 4 |
| 06 | Kubernetes Networking & Services | 6001–6006 | 6 |
| 07 | Kubernetes Storage & Persistence | 7001–7005 | 5 |
| 08 | Kubernetes Security & RBAC | 8001–8005 | 5 |
| 09 | Scheduling, Autoscaling & High Availability | 9001–9006 | 6 |
| 10 | Helm, Kustomize, CRDs & GitOps | 10001–10005 | 5 |
| 11 | Cluster Administration & Lifecycle | 11001–11005 | 5 |
| 12 | Observability & Production Troubleshooting | 12001–12012 | 12 |
| 13 | Production Operations & Capstone | 13001–13003 | 3 |
| **Total** | **14 Modules** | | **76** |

**Note:** The catalogue includes optional cluster installation alternatives and environment-specific advanced labs. Not every learner must complete all 76 labs.

---

# 🎓 Learner Lab Execution Guidelines

| Learning Stage | Requirements |
|---|---|
| Modules 00–01 | Complete prerequisites and Kubernetes fundamentals |
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

---

# 📚 Official Kubernetes Documentation

- [Kubernetes Official Documentation](https://kubernetes.io/docs/)
- [Kubernetes Concepts](https://kubernetes.io/docs/concepts/)
- [Kubernetes Tutorials](https://kubernetes.io/docs/tutorials/)
- [Kubernetes Installation](https://kubernetes.io/docs/setup/)
- [kubectl Reference](https://kubernetes.io/docs/reference/kubectl/)
- [Kubernetes Troubleshooting](https://kubernetes.io/docs/tasks/debug/)
- [K3s Documentation](https://docs.k3s.io/)
- [Minikube Documentation](https://minikube.sigs.k8s.io/docs/)
- [kind Documentation](https://kind.sigs.k8s.io/)
- [kOps Documentation](https://kops.sigs.k8s.io/)
- [Amazon EKS Documentation](https://docs.aws.amazon.com/eks/)
- [Azure AKS Documentation](https://learn.microsoft.com/en-us/azure/aks/)
- [Helm Documentation](https://helm.sh/docs/)
- [Kustomize Documentation](https://kubectl.docs.kubernetes.io/)
- [Argo CD Documentation](https://argo-cd.readthedocs.io/)

---

# 🚀 Begin Your Kubernetes Learning Journey

Start with **Lab 0000 — Verify Linux, Container and Networking Prerequisites**.

Follow the labs in module order, choose a suitable Kubernetes cluster installation method, and complete **Lab 2008 — Verify Kubernetes Cluster and Lab Readiness** before proceeding to application deployment labs.

Progress from foundational Kubernetes administration to production troubleshooting through structured, practical exercises.

---

**Vyomanant Academy**  
*Kubernetes Administration, Engineering & Production Troubleshooting*  
**Master Hands-On Lab Catalogue | Version 2.2**
