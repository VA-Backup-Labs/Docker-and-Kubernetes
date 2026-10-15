
# Kubernetes Administration, Engineering & Production Troubleshooting

**Master Curriculum & Course Table of Contents | Version 2.4**

**Learning Path:** Beginner → Intermediate → Advanced → Production Engineering  
**Delivery:** Instructor-Led | Live Demonstrations | Guided Labs | Self-Paced Practice  
**Total Modules:** 14  
**Lab Catalogue:** 87 Planned Labs  
**Cluster Setup Guides:** 7  
**Capstone Projects:** 1

**Target Audience:** DevOps Engineers, DevSecOps Engineers, Cloud Engineers, Kubernetes Administrators, Platform Engineers, Site Reliability Engineers (SREs), Infrastructure Support Engineers, Application Support Engineers and Forward Deployed Engineers.

---

# 🌍 Program Overview

This program is designed to take learners from foundational Kubernetes concepts to real-world cluster administration, application engineering, security, scaling, monitoring and production troubleshooting.

Every major topic is supported by practical demonstrations, guided exercises or hands-on labs.

The curriculum follows a dependency-based learning approach, ensuring that learners understand the prerequisites before performing practical activities.

**Program Objective:** Develop practical Kubernetes engineering and administration skills, including cluster architecture, application deployment, networking, persistent storage, security, scheduling, scaling, observability, troubleshooting and production operations.

**Training Approach:**

- Beginner-friendly instructor-led explanations
- Structured hands-on learning
- Architecture diagrams and demonstrations
- Local Ubuntu and AWS EC2 Ubuntu environments
- Single-node and multi-node Kubernetes installation options
- Practical Kubernetes administration
- Real-world troubleshooting scenarios
- Production-oriented exercises
- Integrated capstone project

---

# 🧭 Recommended Learning Journey

# Phase 1 — Foundation & Cluster Readiness

**Modules 00–03**

Understand Linux and container prerequisites, learn Kubernetes architecture, install a cluster and begin using kubectl.

# Phase 2 — Application Engineering

**Modules 04–07**

Deploy applications, manage configuration, implement networking and configure persistent storage.

# Phase 3 — Security & Reliability

**Modules 08–09**

Secure Kubernetes environments, control scheduling, implement autoscaling and improve workload availability.

# Phase 4 — Advanced Platform Engineering

**Modules 10–11**

Use Helm, Kustomize and GitOps, and perform Kubernetes cluster administration and lifecycle operations.

# Phase 5 — Production Operations

**Modules 12–13**

Monitor Kubernetes infrastructure, troubleshoot failures and complete an integrated production-oriented capstone project.

---

# 📚 Master Course Table of Contents

| Module | Module Title | Level |
|---|---|---|
| 00 | Kubernetes Orientation & Prerequisites | Foundation |
| 01 | Kubernetes Fundamentals & Cluster Architecture | Beginner |
| 02 | Kubernetes Cluster Installation & Lab Environment Setup | Beginner / Advanced Options |
| 03 | kubectl, YAML & Kubernetes Resource Management | Beginner |
| 04 | Pods, Containers & Workload Management | Beginner–Intermediate |
| 05 | ConfigMaps, Secrets & Resource Management | Intermediate |
| 06 | Kubernetes Networking & Services | Intermediate |
| 07 | Kubernetes Storage & Persistence | Intermediate |
| 08 | Kubernetes Security, RBAC & Access Control | Intermediate–Advanced |
| 09 | Scheduling, Autoscaling & High Availability | Advanced |
| 10 | Helm, Kustomize, CRDs & GitOps | Advanced |
| 11 | Cluster Administration & Lifecycle Management | Advanced |
| 12 | Observability, Monitoring & Production Troubleshooting | Advanced |
| 13 | Production Operations & Capstone Project | Production |

**Training Duration:** Final instructional hours will be determined after allocating time for concepts, demonstrations, mandatory labs, optional infrastructure exercises and assessments.

---

# 🧰 Standard Training Environment

| Component | Recommended Standard |
|---|---|
| Primary Operating System | Ubuntu Server 26.04 LTS |
| Alternative Operating System | Ubuntu Server 24.04 LTS |
| Primary Cloud Environment | AWS EC2 Ubuntu |
| Local Lab Environment | Ubuntu VM / Physical Ubuntu Machine |
| Remote SSH Client | MobaXterm for Windows |
| Interactive Text Editor | vi only |
| Kubernetes CLI | kubectl |
| Container Runtime | containerd / Distribution-Managed Runtime |
| Cluster Deployment Options | K3s, Minikube, kind, kOps, EKS, AKS and kubeadm |
| Configuration Format | YAML |
| Training Documentation | GitHub Markdown |

**Important:**

- Verify Kubernetes distribution and package compatibility with the chosen Ubuntu release.
- Ubuntu 24.04 LTS may be used when Ubuntu 26.04 LTS is not supported by a required component.
- Managed Kubernetes platforms may require provider-specific node operating systems.
- Use `vi` for interactive file editing throughout the training.
- Use MobaXterm for Windows-to-Ubuntu remote SSH sessions.
- Provide verification and cleanup steps for infrastructure-dependent labs.

---

# 🏗️ Module 00 — Kubernetes Orientation & Prerequisites

**Level:** Foundation  
**Delivery Mode:** Theory | Linux Hands-On | Environment Preparation  
**Kubernetes Cluster Required:** No

**Module Objective:** Establish the Linux, containerization, networking and YAML fundamentals necessary for Kubernetes training.

# Topics & Concepts

- 0.1 Course Introduction and Learning Outcomes
- 0.2 Introduction to Cloud-Native Applications
- 0.3 Linux Fundamentals Required for Kubernetes
- 0.4 Containers and Containerization Concepts
- 0.5 Container Images, Registries and Image Pulling
- 0.6 Docker, containerd and Container Runtime Fundamentals
- 0.7 Basic Networking Concepts: IP, DNS, Ports and HTTP
- 0.8 Introduction to YAML
- 0.9 Local and Cloud Lab Infrastructure Requirements
- 0.10 Learner Environment Preparation and Readiness Checklist

# Associated Hands-On Labs

- **Lab 0000:** Verify Linux, Container and Networking Prerequisites
- **Lab 0001:** Prepare Kubernetes Lab Workstation

**Learning Outcome:** Prepare and validate an Ubuntu-based lab environment without installing Kubernetes.

---

# ☸️ Module 01 — Kubernetes Fundamentals & Cluster Architecture

**Level:** Beginner  
**Delivery Mode:** Theory | Architecture Diagrams | Guided Conceptual Exercises  
**Kubernetes Cluster Required:** No

**Module Objective:** Understand Kubernetes architecture, core components, API operations and reconciliation before installing or operating a cluster.

# Topics & Concepts

- 1.1 Introduction to Kubernetes and Container Orchestration
- 1.2 Why Kubernetes? Features, Benefits and Use Cases
- 1.3 Kubernetes Cluster Architecture
- 1.4 Control Plane vs Worker Nodes
- 1.5 Control Plane Components: API Server, etcd, Scheduler and Controller Manager
- 1.6 Worker Node Components: kubelet, kube-proxy and Container Runtime
- 1.7 Container Runtime Interface (CRI)
- 1.8 Container Network Interface (CNI)
- 1.9 Kubernetes Networking and CoreDNS Overview
- 1.10 Kubernetes API and Object Model
- 1.11 Desired State, Controllers and Reconciliation
- 1.12 Declarative vs Imperative Resource Management
- 1.13 Kubernetes Resource Hierarchy
- 1.14 Kubernetes Cluster Communication Flow

# Guided Learning Exercises

- **Lab 1001:** Understand Kubernetes Cluster Architecture and Components
- **Lab 1002:** Explore Kubernetes API, Desired State and Reconciliation Concepts

# Lab 1001 — Guided Activities

- Identify control-plane components.
- Identify worker-node components.
- Understand the responsibilities of Kubernetes components.
- Trace the communication flow for application deployment.
- Explore Kubernetes architecture diagrams.
- Understand how Kubernetes nodes and components interact.

# Lab 1002 — Guided Activities

- Examine example Kubernetes YAML manifests.
- Identify apiVersion, kind, metadata and spec.
- Understand desired state and observed state.
- Explore Kubernetes API request processing conceptually.
- Understand controller reconciliation using illustrated examples.
- Compare imperative and declarative approaches.

**Important:** Module 01 requires no Kubernetes cluster, live API access, running Pods or kubectl execution.

**Learning Outcome:** Explain Kubernetes architecture and reconciliation concepts independently before cluster installation.

---

# 🖥️ Module 02 — Kubernetes Cluster Installation & Lab Environment Setup

**Level:** Beginner to Advanced  
**Delivery Mode:** Instructor Demonstration | Hands-On Cluster Installation  
**Kubernetes Cluster Required:** Created During This Module

**Module Objective:** Provide learners with flexible Kubernetes installation options and establish a verified working cluster for subsequent hands-on labs.

# 2.1 Kubernetes Cluster Installation Overview

- Single-Node vs Multi-Node Kubernetes
- Local vs Cloud Kubernetes
- Self-Managed vs Managed Kubernetes
- Hardware, Networking and Cost Requirements
- Kubernetes Installation Architecture
- Choosing the Appropriate Cluster Deployment Method

# 2.2 Option A — Single-Node Kubernetes Cluster

- 2.2.1 Install K3s on Ubuntu Linux
- 2.2.2 Install Kubernetes using Minikube
- 2.2.3 Install Kubernetes using kind

**Associated Setup Guides:**

- **Lab 2001:** Install Single-Node K3s Kubernetes on Ubuntu
- **Lab 2002:** Install Single-Node Minikube Kubernetes
- **Lab 2003:** Install Single-Node kind Kubernetes

# 2.3 Option B — Multi-Node Kubernetes Cluster

- 2.3.1 Create Kubernetes Cluster using kOps on AWS
- 2.3.2 Create a Multi-Node Amazon EKS Cluster
- 2.3.3 Create a Multi-Node Azure AKS Cluster

**Associated Setup Guides:**

- **Lab 2004:** Install Multi-Node Kubernetes Using kOps on AWS
- **Lab 2005:** Create Multi-Node Amazon EKS Cluster
- **Lab 2006:** Create Multi-Node Azure AKS Cluster

# 2.4 Option C — Kubernetes Installation using kubeadm

- 2.4.1 Prepare Ubuntu Nodes and Container Runtime
- 2.4.2 Initialize the Kubernetes Control Plane
- 2.4.3 Install and Configure CNI Networking
- 2.4.4 Join Worker Nodes
- 2.4.5 Verify Cluster Health

**Associated Setup Guide:**

- **Lab 2007:** Install Kubernetes Cluster Using kubeadm on Ubuntu

# 2.5 Kubernetes Cluster Access

- Install and Configure kubectl
- kubeconfig Files and Kubernetes Contexts
- Remote Cluster Access from Local Machines
- Kubernetes Cluster Health Verification
- Sample Application Deployment
- Cluster Cleanup and Cloud Cost Control

# Mandatory Cluster Readiness Assessment

- **Lab 2008:** Verify Kubernetes Cluster and Lab Readiness

**Readiness Requirements:**

- Kubernetes API server is accessible.
- kubectl connects successfully.
- Nodes are Ready.
- Essential Kubernetes system Pods are healthy.
- A sample application is successfully deployed.
- Application status can be verified.
- Temporary resources can be removed.

**Learning Outcome:** Provision or access a functioning Kubernetes cluster and verify readiness for hands-on learning.

**Important:** Learners select one appropriate installation path. All seven installation guides remain available. The kubeadm installation procedure is also revisited in Module 11 for advanced administration.

---

# ⌨️ Module 03 — kubectl, YAML & Kubernetes Resource Management

**Level:** Beginner  
**Delivery Mode:** Hands-On  
**Kubernetes Cluster Required:** Yes

**Module Objective:** Develop proficiency in using kubectl and YAML manifests to manage Kubernetes API resources.

# Topics & Concepts

- 3.1 Introduction to kubectl
- 3.2 Kubernetes Command Syntax and Essential Commands
- 3.3 Kubernetes YAML Structure and Manifest Files
- 3.4 apiVersion, kind, metadata and spec
- 3.5 Creating Resources Imperatively
- 3.6 Declarative Resource Management using kubectl apply
- 3.7 Kubernetes Namespaces
- 3.8 Labels, Selectors and Annotations
- 3.9 Understanding Kubernetes Resource Status
- 3.10 Inspecting Resources using get, describe and explain
- 3.11 Working with kubectl exec and logs
- 3.12 Kubernetes Events and Basic Diagnostics
- 3.13 Editing, Patching and Deleting Resources
- 3.14 kubectl Output Formats and JSONPath
- 3.15 Context Switching and Multi-Cluster Management

# Associated Hands-On Labs

- **Lab 3001:** Master Essential kubectl Commands
- **Lab 3002:** Create and Manage Kubernetes YAML Manifests
- **Lab 3003:** Manage Namespaces, Labels and Annotations
- **Lab 3004:** Inspect, Edit, Patch and Delete Kubernetes Resources
- **Lab 3005:** Manage Kubernetes Contexts and kubeconfig

**Learning Outcome:** Independently inspect, create, update and manage Kubernetes resources.

---

# 📦 Module 04 — Pods, Containers & Workload Management

**Level:** Beginner–Intermediate  
**Delivery Mode:** Hands-On

**Module Objective:** Deploy, manage, scale, update and troubleshoot Kubernetes application workloads.

# Topics & Concepts

- 4.1 Kubernetes Pods and Containers
- 4.2 Creating Pods using YAML
- 4.3 Pod Lifecycle, Phases and Conditions
- 4.4 Multi-Container Pods
- 4.5 Init Containers and Sidecar Containers
- 4.6 Container Restart Policies
- 4.7 Container Lifecycle Hooks
- 4.8 ReplicaSets and Replica Management
- 4.9 Deployments and Declarative Application Management
- 4.10 Scaling Deployments
- 4.11 Rolling Updates and Rollbacks
- 4.12 Deployment Strategies and Revision History
- 4.13 DaemonSets
- 4.14 StatefulSets and Stable Workload Identity
- 4.15 Jobs and CronJobs
- 4.16 Static Pods
- 4.17 Startup, Liveness and Readiness Probes
- 4.18 Pod Termination and Graceful Shutdown

# Associated Hands-On Labs

- **Lab 4001:** Create and Manage Kubernetes Pods
- **Lab 4002:** Configure Multi-Container Pods and Init Containers
- **Lab 4003:** Create and Manage ReplicaSets and Deployments
- **Lab 4004:** Scale Kubernetes Deployments
- **Lab 4005:** Perform Rolling Updates and Rollbacks
- **Lab 4006:** Configure Liveness, Readiness and Startup Probes
- **Lab 4007:** Deploy DaemonSets and StatefulSets
- **Lab 4008:** Create and Schedule Kubernetes Jobs and CronJobs
- **Lab 4009:** Configure Kubernetes Sidecar Containers and Lifecycle Hooks

**Learning Outcome:** Manage the complete application workload lifecycle on Kubernetes.

---

# ⚙️ Module 05 — ConfigMaps, Secrets & Resource Management

**Level:** Intermediate  
**Delivery Mode:** Hands-On

**Module Objective:** Configure Kubernetes applications and manage resource allocation effectively.

# Topics & Concepts

- 5.1 Kubernetes Application Configuration Management
- 5.2 ConfigMaps
- 5.3 Using ConfigMaps as Environment Variables
- 5.4 Mounting ConfigMaps as Volumes
- 5.5 Kubernetes Secrets and Secret Types
- 5.6 Injecting Secrets into Applications
- 5.7 Private Registry Authentication using imagePullSecrets
- 5.8 CPU and Memory Requests
- 5.9 CPU and Memory Limits
- 5.10 ResourceQuota
- 5.11 LimitRange
- 5.12 Resource Consumption and Capacity Planning
- 5.13 Configuration Updates and Application Rollouts

# Associated Hands-On Labs

- **Lab 5001:** Configure Applications Using ConfigMaps
- **Lab 5002:** Manage Kubernetes Secrets and Private Registry Access
- **Lab 5003:** Configure CPU and Memory Requests and Limits
- **Lab 5004:** Enforce ResourceQuota and LimitRange

**Learning Outcome:** Configure application settings, sensitive information and resource limits.

---

# 🌐 Module 06 — Kubernetes Networking & Services

**Level:** Intermediate  
**Delivery Mode:** Hands-On

**Module Objective:** Understand Kubernetes networking and configure application connectivity, service discovery, routing and traffic isolation.

# Topics & Concepts

- 6.1 Kubernetes Networking Model
- 6.2 Pod-to-Pod Communication
- 6.3 Pod-to-Service Communication
- 6.4 Container Network Interface (CNI)
- 6.5 Calico and Cilium Fundamentals
- 6.6 Kubernetes Services and Service Discovery
- 6.7 ClusterIP Services
- 6.8 NodePort Services
- 6.9 LoadBalancer Services
- 6.10 ExternalName and Headless Services
- 6.11 EndpointSlices and Backend Discovery
- 6.12 kube-proxy and Service Networking
- 6.13 Kubernetes DNS and CoreDNS
- 6.14 Ingress, IngressClass and Ingress Controllers
- 6.15 Gateway API: GatewayClass, Gateway, HTTPRoute and GRPCRoute
- 6.16 NetworkPolicies and Pod Traffic Isolation
- 6.17 IPv4/IPv6 Dual-Stack Networking
- 6.18 Topology-Aware Traffic Distribution
- 6.19 TLS Termination and HTTPS Access
- 6.20 Kubernetes Networking Troubleshooting

# Associated Hands-On Labs

- **Lab 6001:** Explore Pod-to-Pod Network Communication
- **Lab 6002:** Expose Applications Using ClusterIP and NodePort
- **Lab 6003:** Configure LoadBalancer and Headless Services
- **Lab 6004:** Test Kubernetes DNS and Service Discovery
- **Lab 6005:** Configure Ingress and Gateway API Routing
- **Lab 6006:** Implement Kubernetes NetworkPolicies
- **Lab 6007:** Troubleshoot Kubernetes CNI and NetworkPolicy Connectivity

**Learning Outcome:** Configure and troubleshoot Kubernetes application networking.

**Environment Note:** Certain labs require compatible CNI implementations, LoadBalancer integrations, Ingress controllers or Gateway API controllers.

---

# 💾 Module 07 — Kubernetes Storage & Persistence

**Level:** Intermediate  
**Delivery Mode:** Hands-On

**Module Objective:** Configure ephemeral and persistent storage for Kubernetes workloads.

# Topics & Concepts

- 7.1 Kubernetes Storage Architecture
- 7.2 Ephemeral vs Persistent Storage
- 7.3 Kubernetes Volume Types
- 7.4 emptyDir and hostPath
- 7.5 PersistentVolumes (PV)
- 7.6 PersistentVolumeClaims (PVC)
- 7.7 PV/PVC Binding and Lifecycle
- 7.8 StorageClasses
- 7.9 Static Volume Provisioning
- 7.10 Dynamic Volume Provisioning
- 7.11 Container Storage Interface (CSI)
- 7.12 Storage Access Modes and Reclaim Policies
- 7.13 Volume Expansion
- 7.14 VolumeSnapshots and VolumeSnapshotClasses
- 7.15 StatefulSets with Persistent Storage
- 7.16 Storage Troubleshooting and Recovery

# Associated Hands-On Labs

- **Lab 7001:** Configure emptyDir and hostPath Volumes
- **Lab 7002:** Create PersistentVolumes and PersistentVolumeClaims
- **Lab 7003:** Configure StorageClasses and Dynamic Provisioning
- **Lab 7004:** Deploy StatefulSets with Persistent Storage
- **Lab 7005:** Verify Data Persistence and Troubleshoot Storage
- **Lab 7006:** Configure Kubernetes Volume Expansion and VolumeSnapshots

**Learning Outcome:** Manage persistent data and storage lifecycles.

**Environment Note:** Volume expansion and snapshots require compatible storage provisioning and CSI capabilities.

---

# 🔐 Module 08 — Kubernetes Security, RBAC & Access Control

**Level:** Intermediate–Advanced  
**Delivery Mode:** Hands-On

**Module Objective:** Implement Kubernetes authorization, workload security and administrative security controls.

# Topics & Concepts

- 8.1 Kubernetes Security Architecture
- 8.2 Authentication vs Authorization
- 8.3 ServiceAccounts and Workload Identity
- 8.4 Role-Based Access Control (RBAC)
- 8.5 Roles and ClusterRoles
- 8.6 RoleBindings and ClusterRoleBindings
- 8.7 Least-Privilege Access
- 8.8 SecurityContext
- 8.9 Linux Capabilities and Privilege Restrictions
- 8.10 Pod Security Standards
- 8.11 Pod Security Admission
- 8.12 Admission Controllers
- 8.13 Mutating and Validating Admission Webhooks
- 8.14 Secrets Encryption at Rest
- 8.15 Kubernetes Certificates and CertificateSigningRequests
- 8.16 Kubernetes Audit Logging
- 8.17 Container Image Security
- 8.18 NetworkPolicy as a Security Control

# Associated Hands-On Labs

- **Lab 8001:** Create and Manage Kubernetes ServiceAccounts
- **Lab 8002:** Configure Roles, RoleBindings and RBAC
- **Lab 8003:** Implement ClusterRoles and ClusterRoleBindings
- **Lab 8004:** Secure Containers Using SecurityContext
- **Lab 8005:** Enforce Pod Security Admission and Least Privilege
- **Lab 8006:** Configure Kubernetes Secrets Encryption at Rest
- **Lab 8007:** Configure Kubernetes Audit Logging

**Learning Outcome:** Secure Kubernetes access and workloads using practical security controls.

**Environment Note:** Encryption-at-rest and control-plane auditing exercises require appropriate self-managed cluster access.

---

# 📈 Module 09 — Kubernetes Scheduling, Autoscaling & High Availability

**Level:** Advanced  
**Delivery Mode:** Hands-On | Multi-Node Exercises

**Module Objective:** Manage Kubernetes scheduling policies, autoscaling and application availability.

# Topics & Concepts

- 9.1 Kubernetes Scheduler Architecture
- 9.2 Resource Requests and Scheduling
- 9.3 Node Labels and nodeSelector
- 9.4 Node Affinity and Anti-Affinity
- 9.5 Pod Affinity and Anti-Affinity
- 9.6 Node Taints and Pod Tolerations
- 9.7 PriorityClass and Pod Preemption
- 9.8 Topology Spread Constraints
- 9.9 PodDisruptionBudgets
- 9.10 Horizontal Pod Autoscaler (HPA)
- 9.11 Metrics Server and Resource Metrics
- 9.12 Vertical Pod Autoscaler (VPA)
- 9.13 Node Autoscaling
- 9.14 Cluster Autoscaler and Karpenter
- 9.15 Control Plane and Workload High Availability
- 9.16 Node Maintenance: Cordon, Drain and Uncordon
- 9.17 Troubleshooting Scheduling Failures

# Associated Hands-On Labs

- **Lab 9001:** Schedule Pods Using nodeSelector and Node Affinity
- **Lab 9002:** Configure Pod Affinity and Anti-Affinity
- **Lab 9003:** Configure Node Taints and Pod Tolerations
- **Lab 9004:** Configure Horizontal Pod Autoscaler
- **Lab 9005:** Implement PodDisruptionBudgets and Topology Spread
- **Lab 9006:** Perform Node Cordon, Drain and Uncordon
- **Lab 9007:** Configure Vertical Pod Autoscaler
- **Lab 9008:** Explore Kubernetes Node Autoscaling with Karpenter or Cluster Autoscaler

**Learning Outcome:** Improve Kubernetes workload placement, scalability and resilience.

**Environment Note:** Multi-node and cloud-specific infrastructure is required for certain exercises. VPA requires the appropriate components.

---

# 🚀 Module 10 — Helm, Kustomize, CRDs & GitOps

**Level:** Advanced  
**Delivery Mode:** Hands-On

**Module Objective:** Package, customize, automate and progressively deliver Kubernetes applications.

# Topics & Concepts

- 10.1 Kubernetes Application Packaging
- 10.2 Helm Architecture and Fundamentals
- 10.3 Helm Charts, Templates and Values
- 10.4 Helm Installation, Upgrade and Rollback
- 10.5 Helm Chart Development
- 10.6 Kustomize Fundamentals
- 10.7 Base Manifests and Environment Overlays
- 10.8 CustomResourceDefinitions (CRDs)
- 10.9 Custom Resources and Controllers
- 10.10 Kubernetes Operator Pattern
- 10.11 API Aggregation
- 10.12 Admission Webhooks and Policy Extensions
- 10.13 GitOps Fundamentals
- 10.14 Argo CD and Flux
- 10.15 Kubernetes CI/CD Integration
- 10.16 Rolling, Blue-Green and Canary Deployments

# Associated Hands-On Labs

- **Lab 10001:** Install and Manage Applications Using Helm
- **Lab 10002:** Create and Customize Helm Charts
- **Lab 10003:** Manage Kubernetes Manifests Using Kustomize
- **Lab 10004:** Create and Explore Kubernetes CustomResourceDefinitions
- **Lab 10005:** Implement GitOps Deployment Using Argo CD
- **Lab 10006:** Implement Blue-Green and Canary Deployment Strategies

**Learning Outcome:** Implement Kubernetes application packaging and modern deployment workflows.

---

# 🛠️ Module 11 — Kubernetes Cluster Administration & Lifecycle Management

**Level:** Advanced  
**Delivery Mode:** Advanced Cluster Administration Labs

**Module Objective:** Perform cluster lifecycle operations, upgrades, maintenance, backup, recovery and administrative troubleshooting.

# Topics & Concepts

- 11.1 Cluster Lifecycle and Operational Responsibilities
- 11.2 Self-Managed vs Managed Kubernetes
- 11.3 kubeadm Cluster Installation and Configuration
- 11.4 Control Plane Initialization and Worker Node Joining
- 11.5 Kubernetes Container Runtime and CNI Configuration
- 11.6 Kubernetes Certificates and kubeconfig Administration
- 11.7 Cluster Versioning and Compatibility
- 11.8 Kubernetes Cluster Upgrades
- 11.9 Node Addition, Replacement and Removal
- 11.10 etcd Architecture and Operations
- 11.11 etcd Snapshot, Backup and Restore
- 11.12 Control Plane Recovery
- 11.13 kubelet and systemd Troubleshooting
- 11.14 Cluster Capacity Management
- 11.15 Kubernetes API Deprecations and Compatibility Checks
- 11.16 Amazon EKS Administration
- 11.17 Cluster Maintenance and Operational Checklists

# Associated Hands-On Labs

- **Lab 11001:** Administer kubeadm Control Plane and Worker Nodes
- **Lab 11002:** Manage Kubernetes Certificates and kubeconfig
- **Lab 11003:** Perform Kubernetes Cluster and Node Upgrades
- **Lab 11004:** Perform etcd Backup and Restore
- **Lab 11005:** Troubleshoot kubelet and Node Services

**Learning Outcome:** Administer and recover Kubernetes clusters using appropriate operational procedures.

**Environment Note:** These labs primarily require self-managed Kubernetes on Ubuntu VMs or AWS EC2. Managed control-plane operations differ for EKS and AKS.

---

# 📊 Module 12 — Observability, Monitoring & Production Troubleshooting

**Level:** Advanced  
**Delivery Mode:** Monitoring | Incident Simulation | Root Cause Analysis

**Module Objective:** Diagnose and resolve Kubernetes application, networking, storage, security and infrastructure failures.

# Topics & Concepts

- 12.1 Kubernetes Observability Fundamentals
- 12.2 Metrics, Logs, Events and Traces
- 12.3 kubectl top and Metrics Server
- 12.4 Kubernetes Resource Events and Status
- 12.5 Container Logs and Previous Container Logs
- 12.6 kubelet and Kubernetes Component Logs
- 12.7 Prometheus and Grafana Monitoring
- 12.8 Kubernetes Alerting Fundamentals
- 12.9 Troubleshooting Pod Pending
- 12.10 Troubleshooting CrashLoopBackOff
- 12.11 Troubleshooting ImagePullBackOff and ErrImagePull
- 12.12 Troubleshooting ContainerCreating
- 12.13 Troubleshooting OOMKilled
- 12.14 Troubleshooting Pods Running but Applications Unreachable
- 12.15 Troubleshooting Deployment Rollout Failures
- 12.16 Troubleshooting Services and DNS
- 12.17 Troubleshooting Ingress and Gateway Routing
- 12.18 Troubleshooting CNI and NetworkPolicies
- 12.19 Troubleshooting PV/PVC and Volume Mount Failures
- 12.20 Troubleshooting Node NotReady and Resource Pressure
- 12.21 Troubleshooting RBAC and API Authorization
- 12.22 Incident Investigation and Root Cause Analysis

# Associated Hands-On Labs

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

**Learning Outcome:** Perform structured Kubernetes diagnostics, identify root causes and verify recovery.

---

# 🏆 Module 13 — Kubernetes Production Operations & Capstone Project

**Level:** Production  
**Delivery Mode:** Integrated Practical Assessment

**Module Objective:** Apply all major Kubernetes engineering and operational skills to a production-oriented application environment.

# Topics & Concepts

- 13.1 Production Kubernetes Architecture Review
- 13.2 Multi-Tier Application Deployment Design
- 13.3 Namespace and Environment Organization
- 13.4 Configuration and Secret Management
- 13.5 Resource Requests, Limits and Scheduling
- 13.6 Persistent Storage and Stateful Databases
- 13.7 Internal and External Application Connectivity
- 13.8 Gateway/Ingress Routing and TLS
- 13.9 RBAC and Workload Security Hardening
- 13.10 NetworkPolicies and Traffic Isolation
- 13.11 Autoscaling and High-Availability Validation
- 13.12 Monitoring, Logging and Dashboards
- 13.13 Application Upgrades and Rollbacks
- 13.14 Backup, Restore and Disaster Recovery
- 13.15 Failure Simulation and Incident Response
- 13.16 Production Readiness Assessment
- 13.17 Capstone Demonstration, Documentation and Technical Evaluation

# Associated Hands-On Labs

- **Lab 13001:** Deploy Production-Oriented Multi-Tier Application
- **Lab 13002:** Validate Security, Scaling, Monitoring and Recovery
- **Lab 13003:** Kubernetes Production Capstone Assessment

# Final Capstone Requirements

- Frontend and backend applications
- Stateful database with persistent storage
- ConfigMaps, Secrets and ServiceAccounts
- Internal and external Services
- Gateway/Ingress routing and TLS
- CPU and memory requests and limits
- RBAC and least-privilege configuration
- Pod Security settings
- NetworkPolicies
- Health probes
- Autoscaling where supported
- Monitoring and operational dashboards
- Application rolling updates and rollback
- Backup and recovery validation
- Failure simulation and troubleshooting
- Technical documentation and operational runbooks

**Final Assessment:** Evaluate the learner's ability to deploy, administer, secure, monitor and troubleshoot a Kubernetes application independently.

---

# 🧪 Lab Execution and Dependency Rules

- Module 00 requires Ubuntu or equivalent prerequisite access but no Kubernetes cluster.
- Module 01 is cluster-independent and uses architecture diagrams, YAML examples and conceptual exercises.
- Module 02 is the first module requiring Kubernetes installation and live cluster execution.
- Learners must complete one cluster setup guide and pass Lab 2008.
- Module 03 begins routine kubectl-based Kubernetes operations.
- Modules 04–08 cover core application engineering and security.
- Module 09 includes multi-node and infrastructure-dependent exercises.
- Module 10 introduces application packaging and GitOps.
- Module 11 uses suitable administrative environments for advanced operations.
- Module 12 uses isolated clusters for fault simulation and troubleshooting.
- Module 13 integrates all applicable competencies in the final capstone.

---

# 📚 Official Documentation

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

**Vyomanant Academy**  
*Kubernetes Administration, Engineering & Production Troubleshooting*  
**Master Curriculum & Course Table of Contents | Version 2.4**
