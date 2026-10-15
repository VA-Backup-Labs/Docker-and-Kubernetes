# TOC-Kubernetes Administration, Engineering & Production Troubleshooting

Master Curriculum & Hands-On Lab Catalogue | Version 2.0

- **Learning Path:** Beginner → Intermediate → Advanced → Production Engineering 
- **Delivery:** Instructor-Led | Live Demonstrations | Guided Labs | Self-Paced Practice Target 
- **Audience:** DevOps Engineers, Cloud Engineers, Kubernetes Administrators, Platform Engineers, SREs and Technical Support Engineers

# 🌍 Program Overview

This program is designed to take learners from foundational Kubernetes concepts to real-world cluster administration, application engineering, security, scaling, monitoring and production troubleshooting.

Every major topic is supported by practical demonstrations and hands-on exercises.

#### 14 - Modules | 7  - Cluster Setup Guides | 1  - Capstone Project

Hands-on learning • Flexible environments • Production-oriented skills

# 🧭 Recommended Learning Journey

### Phase 1 — Foundation & Cluster Readiness (Modules 00–03)

Understand Kubernetes, install a cluster and learn kubectl

### Phase 2 — Application Engineering (Modules 04–07)

Deploy, configure, connect and persist applications

### Phase 3 — Security & Reliability (Modules 08–09)

Secure workloads, control scheduling and implement scaling

### Phase 4 — Advanced Platform Engineering (Modules 10–11)

Use Helm, GitOps and administer Kubernetes clusters

### Phase 5 — Production Operations (Modules 12–13)

Monitor, troubleshoot and complete the capstone

# 📚 Part A — Master Course Table of Contents

| Module | Module Title                                           | Level                       |
| ------ | ------------------------------------------------------ | --------------------------- |
| 00     | Kubernetes Orientation & Prerequisites                 | Foundation                  |
| 01     | Kubernetes Fundamentals & Cluster Architecture         | Beginner                    |
| 02     | Kubernetes Cluster Installation & Lab Setup            | Beginner / Advanced Options |
| 03     | kubectl, YAML & Kubernetes Resource Management         | Beginner                    |
| 04     | Pods, Containers & Workload Management                 | Beginner–Intermediate       |
| 05     | ConfigMaps, Secrets & Resource Management              | Intermediate                |
| 06     | Kubernetes Networking & Services                       | Intermediate                |
| 07     | Kubernetes Storage & Persistence                       | Intermediate                |
| 08     | Kubernetes Security, RBAC & Access Control             | Intermediate–Advanced       |
| 09     | Scheduling, Autoscaling & High Availability            | Advanced                    |
| 10     | Helm, Kustomize, CRDs & GitOps                         | Advanced                    |
| 11     | Cluster Administration & Lifecycle Management          | Advanced                    |
| 12     | Observability, Monitoring & Production Troubleshooting | Advanced                    |
| 13     | Production Operations & Capstone Project               | Production                  |

The previous 60-hour allocation is not retained as a final promise. The revised structure includes additional foundational and setup activities; duration should be finalized against instructor demonstrations and required labs.

# 🏗️ Module 00 — Kubernetes Orientation & Prerequisites

Foundation | Self-Paced Preparation

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

# ☸️ Module 01 — Kubernetes Fundamentals & Cluster Architecture

Beginner | Concepts and Architecture

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

# 🖥️ Module 02 — Kubernetes Cluster Installation & Lab Environment Setup

Mandatory Cluster Readiness | Learner-Selected Setup Path

2.1 Kubernetes Cluster Installation Overview

- Single-Node vs Multi-Node Kubernetes
- Local vs Cloud Kubernetes
- Self-Managed vs Managed Kubernetes
- Hardware, Networking and Cost Requirements

2.2 Option A — Single-Node Kubernetes Cluster

- 2.2.1 Install K3s on Ubuntu Linux
- 2.2.2 Install Kubernetes using Minikube
- 2.2.3 Install Kubernetes using kind

2.3 Option B — Multi-Node Kubernetes Cluster

- 2.3.1 Create Kubernetes Cluster using kOps on AWS
- 2.3.2 Create a Multi-Node Amazon EKS Cluster
- 2.3.3 Create a Multi-Node Azure AKS Cluster

2.4 Option C — Kubernetes Installation using kubeadm

- 2.4.1 Prepare Ubuntu Nodes and Container Runtime
- 2.4.2 Initialize the Kubernetes Control Plane
- 2.4.3 Install and Configure CNI Networking
- 2.4.4 Join Worker Nodes
- 2.4.5 Verify Cluster Health

2.5 Kubernetes Cluster Access

- Install and Configure kubectl
- kubeconfig Files and Kubernetes Contexts
- Remote Cluster Access from Local Machines
- Kubernetes Cluster Health Verification
- Sample Application Deployment
- Cluster Cleanup and Cloud Cost Control

Learners complete one setup path. All seven guides remain available in the lab repository. The kubeadm installation lab is introduced here as an option and taught in depth in Module 11.

# ⌨️ Module 03 — kubectl, YAML & Kubernetes Resource Management

Beginner | Core Kubernetes Operations

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

# 📦 Module 04 — Pods, Containers & Workload Management

Beginner–Intermediate | Kubernetes Applications

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

# ⚙️ Module 05 — ConfigMaps, Secrets & Resource Management

Intermediate | Application Configuration

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

# 🌐 Module 06 — Kubernetes Networking & Services

Intermediate | Application Connectivity

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

# 💾 Module 07 — Kubernetes Storage & Persistence

Intermediate | Stateful Workloads

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

# 🔐 Module 08 — Kubernetes Security, RBAC & Access Control

Intermediate–Advanced | Kubernetes Security

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

# 📈 Module 09 — Scheduling, Autoscaling & High Availability

Advanced | Workload Reliability

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

# 🚀 Module 10 — Helm, Kustomize, CRDs & GitOps

Advanced | Application Delivery and Extensibility

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

# 🛠️ Module 11 — Cluster Administration & Lifecycle Management

Advanced | Kubernetes Administration

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

# 📊 Module 12 — Observability, Monitoring & Production Troubleshooting

Advanced | Production Troubleshooting

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

# 🏆 Module 13 — Production Operations & Capstone Project

Production | Integrated Practical Assessment

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
