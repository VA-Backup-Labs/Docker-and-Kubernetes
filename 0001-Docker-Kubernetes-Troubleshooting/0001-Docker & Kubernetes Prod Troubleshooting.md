# 🐳☸️ Docker & Kubernetes Production Troubleshooting Labs

Two independent, hands-on troubleshooting lab series | Beginner to Advanced | DevOps, SRE & Cloud Engineers

The focus will be on real-world production incidents, where learners diagnose broken environments, identify root causes, implement fixes, and verify recovery.

Both technologies will have completely separate lab tracks, suitable for GitHub repositories, instructor-led corporate training, and self-paced learning.

# 🐳 Track 1 — Docker Troubleshooting Labs

[Violet 6.0 Loyalty CRM | Boost Customer Loyalty Today — SimplifiJohan](https://images.openai.com/static-rsc-4/D6ZDPBXxXeE0aIBuzNttmM7WRxJrEkEBl9qXTLsQX6cTyll0Ui-weVIllJ4ImSO7e2ta5RwHuESzaquDyqOUjhLMzgyTxW7u_AJduRPh5le9j5ATeFeaMB4qSdl5riSL0LikmkKH_WDc4zE9Oskxs2juKxmrv_dvQ5VIKARG6xg?purpose=inline)

## Docker Container Troubleshooting

12 hands-on labs · Beginner → Advanced

Diagnose container lifecycle, networking, storage, resource, and application failures.

| Lab | Troubleshooting Scenario                                   | Level        |
| --- | ---------------------------------------------------------- | ------------ |
| D01 | Docker Daemon Not Running or Unreachable                   | Beginner     |
| D02 | Docker Container Exits Immediately After Starting          | Beginner     |
| D03 | Docker Container Keeps Restarting                          | Beginner     |
| D04 | Docker Image Pull Failures and Registry Authentication     | Beginner     |
| D05 | Docker Build Failures and Dockerfile Debugging             | Intermediate |
| D06 | Docker Container Port Mapping and Connectivity Issues      | Intermediate |
| D07 | Docker DNS Resolution and Container Networking Failures    | Intermediate |
| D08 | Docker Volume Mount and File Permission Issues             | Intermediate |
| D09 | Docker Container High CPU and Memory Consumption           | Intermediate |
| D10 | Docker Container Out-of-Memory (OOM) Failures              | Advanced     |
| D11 | Docker Disk Space Exhaustion and Log Growth                | Advanced     |
| D12 | Docker Compose Multi-Container Application Troubleshooting | Advanced     |

Practical approach: Each lab will introduce a deliberately broken Docker environment and guide learners through investigation using tools such as `docker logs`, `docker inspect`, `docker stats`, `docker exec`, and `docker events`.

# ☸️ Track 2 — Kubernetes Troubleshooting Labs

[How to use SSL (letsencrypt) with Digital Ocean Kubernetes, Cert-Manager and nginx-ingress | by Max | Medium](https://images.openai.com/static-rsc-4/r29ptdLPQ8sBjQ3UoBeoX4vcaSb_3yJKGFqRxCJhiB8gRbJKCR7PB3rFtyiS9i_osxSf1Av60HEq_NWiNWX5zLEwyWX7jumv_CtRD0WWrO76UCfG8gnNnPqdwn97RMPMS7qordPM9zp93uexTvdIpNhV-RV5dJrkh2rjtzzc0Og?purpose=inline)

## Kubernetes Cluster & Application Troubleshooting

18 hands-on labs · Beginner → Advanced

Diagnose Pods, scheduling, networking, storage, deployments, and cluster failures.

| Lab | Troubleshooting Scenario                                   | Level        |
| --- | ---------------------------------------------------------- | ------------ |
| K01 | Kubernetes Pod Stuck in Pending State                      | Beginner     |
| K02 | Kubernetes Pod CrashLoopBackOff Troubleshooting            | Beginner     |
| K03 | Kubernetes ImagePullBackOff and ErrImagePull               | Beginner     |
| K04 | Kubernetes Pod Stuck in ContainerCreating                  | Beginner     |
| K05 | Kubernetes Pod Running but Application Not Accessible      | Intermediate |
| K06 | Kubernetes Service Not Routing Traffic to Pods             | Intermediate |
| K07 | Kubernetes DNS Resolution and CoreDNS Troubleshooting      | Intermediate |
| K08 | Kubernetes ConfigMap and Secret Configuration Failures     | Intermediate |
| K09 | Kubernetes Liveness, Readiness and Startup Probe Failures  | Intermediate |
| K10 | Kubernetes OOMKilled and Memory Limit Issues               | Intermediate |
| K11 | Kubernetes CPU Throttling and Resource Pressure            | Intermediate |
| K12 | Kubernetes PVC Stuck in Pending and Storage Mount Failures | Intermediate |
| K13 | Kubernetes Deployment Rollout Failures and Rollbacks       | Intermediate |
| K14 | Kubernetes Ingress Routing and HTTP 404/503 Errors         | Advanced     |
| K15 | Kubernetes Node NotReady Troubleshooting                   | Advanced     |
| K16 | Kubernetes Scheduling Failures, Taints and Affinity        | Advanced     |
| K17 | Kubernetes NetworkPolicy Connectivity Failures             | Advanced     |
| K18 | End-to-End Kubernetes Production Incident Troubleshooting  | Advanced     |

Practical approach: Learners will troubleshoot intentionally misconfigured Kubernetes resources using `kubectl describe`, `kubectl logs`, `kubectl get events`, `kubectl exec`, `kubectl top`, and rollout diagnostics.

# 🏗️ Recommended Lab Environments

| Docker Labs           | Kubernetes Labs    |
| --------------------- | ------------------ |
| Ubuntu Linux VM       | Minikube or Kind   |
| Docker Engine         | kubectl CLI        |
| Docker Compose plugin | Kubernetes cluster |
| VS Code + Terminal    | VS Code + YAML     |
| Optional AWS EC2      | Optional AWS EKS   |

For beginner labs, local environments will be preferred to minimize AWS costs. Advanced Kubernetes labs may require multi-node clusters or EKS, depending on the failure scenario.

# 🧪 Standard Format for Every Troubleshooting Lab

Every lab will follow a consistent incident-based learning methodology:

1\. Introduce the Incident

Describe the production symptom and expected behavior.

2\. Reproduce the Failure

Create a deliberately broken container or Kubernetes resource.

3\. Investigate and Diagnose

Collect logs, events, status information, and diagnostic evidence.

4\. Fix and Verify

Resolve the root cause and confirm application recovery.

5\. Document and Clean Up

Record lessons learned, prevention techniques, and remove resources.

Each guide will also include prerequisites, beginner-friendly command explanations, expected outputs, common mistakes, root-cause analysis, and official documentation references.

# 📁 GitHub Repository Structure

```
Docker-Kubernetes-Troubleshooting/
│
├── Docker-Troubleshooting/
│   ├── D01-Docker-Daemon-Troubleshooting.md
│   ├── D02-Container-Exit-Troubleshooting.md
│   ├── ...
│   └── D12-Docker-Compose-Troubleshooting.md
│
└── Kubernetes-Troubleshooting/
    ├── K01-Pod-Pending-Troubleshooting.md
    ├── K02-CrashLoopBackOff-Troubleshooting.md
    ├── ...
    └── K18-End-to-End-Incident.md
```

# 🚀 Start Writing the Labs

I recommend developing the Docker track first, followed by Kubernetes, one complete lab at a time. This builds troubleshooting skills progressively without repeating foundational explanations.

🐳 Start Docker — Lab D01

Docker Daemon Not Running or Unreachable

Start&#x20;

☸️ Start Kubernetes — Lab K01

Kubernetes Pod Stuck in Pending State

Start&#x20;

Total: 30 practical troubleshooting labs, with separate Docker and Kubernetes learning paths.
