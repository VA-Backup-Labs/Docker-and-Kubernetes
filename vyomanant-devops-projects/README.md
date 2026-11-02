# Vyomanant Academy — Docker & DevOps Hands-on Projects

**Learn. Build. Grow.**  
Website: https://www.vyomanant.com · Email: academy@vyomanant.com

A beginner-friendly, step-by-step lab guide to build, run, test, and manage **nine independent Dockerized web applications** on an **Ubuntu AWS EC2 instance**. Each project is a static HTML/CSS/JavaScript application served by **NGINX Alpine**.

> **Training use only.** Banking, shopping, finance, and student information are simulated. Browser-local storage is not a shared database. Do not enter real financial, personal, or sensitive data.

## Contents

1. [Applications and ports](#1-applications-and-ports)
2. [Prerequisites and AWS security](#2-prerequisites-and-aws-security)
3. [Install Docker on Ubuntu](#3-install-docker-on-ubuntu)
4. [Clone the GitHub repository](#4-clone-the-github-repository)
5. [Understand the three files](#5-understand-the-three-files)
6. [Run all nine projects one by one](#6-run-all-nine-projects-one-by-one)
7. [Verify and troubleshoot](#7-verify-and-troubleshoot)
8. [Stop, restart, and delete containers](#8-stop-restart-and-delete-containers)
9. [Pull changes and redeploy](#9-pull-changes-and-redeploy)
10. [Classroom exercises](#10-classroom-exercises)

## 1. Applications and ports

| # | Folder | Application | Docker image | Container name | EC2 host port |
|---|---|---|---|---|---:|
| 1 | `01-smart-calculator` | Smart Calculator | `vyomanant-calculator:v1` | `calculator-app` | 8081 |
| 2 | `02-finance-manager` | Finance Manager | `vyomanant-finance:v1` | `finance-app` | 8082 |
| 3 | `03-food-delivery` | Food Delivery | `vyomanant-food:v1` | `food-app` | 8083 |
| 4 | `04-games-arcade` | Games Arcade | `vyomanant-games:v1` | `games-app` | 8084 |
| 5 | `05-ecommerce-store` | E-Commerce Store | `vyomanant-ecommerce:v1` | `ecommerce-app` | 8085 |
| 6 | `06-task-manager` | Task Manager | `vyomanant-taskflow:v1` | `task-manager` | 8086 |
| 7 | `07-banking-simulator` | Banking Simulator | `vyomanant-banking:v1` | `banking-app` | 8087 |
| 8 | `08-student-management` | Student Management | `vyomanant-students:v1` | `student-management` | 8088 |
| 9 | `09-devops-demo-portal` | DevOps Demo Portal | `vyomanant-demo-portal:v1` | `vyomanant-demo-portal` | 8090 |

**Port mapping explained:** `-p 8081:80` means the browser connects to port **8081 on EC2**, which Docker forwards to **port 80 inside the NGINX container**. Port **8089 is unused** in this collection. The existing Academy website may use port **8080**, but it is not part of these nine projects.

## 2. Prerequisites and AWS security

- An AWS EC2 instance running a supported Ubuntu release, with SSH access and enough memory/disk space for nine lightweight containers.
- A security group that permits **SSH TCP 22 from your IP only**.
- For browser testing, permit only the **specific app ports** you need (8081–8088 and 8090), preferably **from your IP only**. Do **not** expose every port to `0.0.0.0/0` just for a lab.
- Git and Docker installed on EC2. Internet access to download packages and images.
- Your EC2 **public IPv4 address** or public DNS. Public IPs may change when an instance stops and starts unless you use an Elastic IP.

> **Security note:** These demo sites use plain HTTP and have no production authentication. Avoid real user data. For a public deployment, use HTTPS, access controls, and a reverse proxy instead of exposing numerous ports.

## 3. Install Docker on Ubuntu

Connect to EC2 over SSH, then run:

```bash
sudo apt update
sudo apt install -y docker.io git
sudo systemctl enable --now docker
sudo docker --version
git --version
```

For simplicity, all Docker commands in this guide use **`sudo docker`**. This avoids requiring a logout/login to activate Docker group membership. If Docker is already installed, you can skip installation.

Check Docker is working:

```bash
sudo docker info
sudo docker ps
```

**Explanation:** `docker info` checks Docker Engine connectivity; `docker ps` lists running containers.

## 4. Clone the GitHub repository

```bash
cd ~
git clone https://github.com/VA-Backup-Labs/Docker-and-Kubernetes.git
cd Docker-and-Kubernetes/vyomanant-devops-projects
pwd
ls -la
```

> If the GitHub repository is private, cloning requires authentication, for example an authorized GitHub credential or SSH key. Do not put personal access tokens into command history or the README.

Expected structure:

```text
vyomanant-devops-projects/
├── 01-smart-calculator/
├── 02-finance-manager/
├── 03-food-delivery/
├── 04-games-arcade/
├── 05-ecommerce-store/
├── 06-task-manager/
├── 07-banking-simulator/
├── 08-student-management/
├── 09-devops-demo-portal/
└── README.md
```

Each application folder should contain `index.html`, `Dockerfile`, and `nginx.conf`.

## 5. Understand the three files

| File | Purpose |
|---|---|
| `index.html` | The application UI, CSS styling, and JavaScript functionality. |
| `Dockerfile` | Instructions to build an image containing NGINX and the application. |
| `nginx.conf` | NGINX configuration for serving the static application on container port 80. |

**Important Docker commands:**

- `docker build -t IMAGE:TAG .` builds an image from the **current directory** (`.`).
- `docker run -d --name NAME --restart unless-stopped -p HOST:80 IMAGE:TAG` starts a detached container and publishes its web port.
- `docker ps` lists running containers; `docker ps -a` includes stopped containers.
- `docker logs NAME` shows logs; `docker exec NAME nginx -t` checks NGINX configuration.
- `curl -I http://localhost:PORT` tests HTTP response headers **from the EC2 instance**.

## 6. Run all nine projects one by one

Start from the repository root:

```bash
cd ~/Docker-and-Kubernetes/vyomanant-devops-projects
```

Run each project's two commands below. Each `cd` starts at the repository root, so you can copy any block independently.

### Project 1 — Smart Calculator (8081)

A browser-based calculator for learning Docker image builds and host-to-container port mapping.

```bash
cd ~/Docker-and-Kubernetes/vyomanant-devops-projects/01-smart-calculator
sudo docker build -t vyomanant-calculator:v1 .
sudo docker run -d --name calculator-app --restart unless-stopped -p 8081:80 vyomanant-calculator:v1
curl -I http://localhost:8081
```

Open: `http://YOUR_EC2_PUBLIC_IP:8081`

### Project 2 — Finance Manager (8082)

A personal finance demo for testing container isolation and browser-local data persistence.

```bash
cd ~/Docker-and-Kubernetes/vyomanant-devops-projects/02-finance-manager
sudo docker build -t vyomanant-finance:v1 .
sudo docker run -d --name finance-app --restart unless-stopped -p 8082:80 vyomanant-finance:v1
curl -I http://localhost:8082
```

Open: `http://YOUR_EC2_PUBLIC_IP:8082`

### Project 3 — Food Delivery (8083)

A fictional ordering interface for practicing repeatable application deployment.

```bash
cd ~/Docker-and-Kubernetes/vyomanant-devops-projects/03-food-delivery
sudo docker build -t vyomanant-food:v1 .
sudo docker run -d --name food-app --restart unless-stopped -p 8083:80 vyomanant-food:v1
curl -I http://localhost:8083
```

Open: `http://YOUR_EC2_PUBLIC_IP:8083`

### Project 4 — Games Arcade (8084)

A collection of browser games for exploring static asset hosting and JavaScript interactivity.

```bash
cd ~/Docker-and-Kubernetes/vyomanant-devops-projects/04-games-arcade
sudo docker build -t vyomanant-games:v1 .
sudo docker run -d --name games-app --restart unless-stopped -p 8084:80 vyomanant-games:v1
curl -I http://localhost:8084
```

Open: `http://YOUR_EC2_PUBLIC_IP:8084`

### Project 5 — E-Commerce Store (8085)

A fictional storefront with cart and simulated checkout. No real payments.

```bash
cd ~/Docker-and-Kubernetes/vyomanant-devops-projects/05-ecommerce-store
sudo docker build -t vyomanant-ecommerce:v1 .
sudo docker run -d --name ecommerce-app --restart unless-stopped -p 8085:80 vyomanant-ecommerce:v1
curl -I http://localhost:8085
```

Open: `http://YOUR_EC2_PUBLIC_IP:8085`

### Project 6 — Task Manager (8086)

A Kanban-style task manager for practicing browser state and independent container deployments.

```bash
cd ~/Docker-and-Kubernetes/vyomanant-devops-projects/06-task-manager
sudo docker build -t vyomanant-taskflow:v1 .
sudo docker run -d --name task-manager --restart unless-stopped -p 8086:80 vyomanant-taskflow:v1
curl -I http://localhost:8086
```

Open: `http://YOUR_EC2_PUBLIC_IP:8086`

### Project 7 — Banking Simulator (8087)

A fictional banking interface for demonstrating transactions without real banking integration.

```bash
cd ~/Docker-and-Kubernetes/vyomanant-devops-projects/07-banking-simulator
sudo docker build -t vyomanant-banking:v1 .
sudo docker run -d --name banking-app --restart unless-stopped -p 8087:80 vyomanant-banking:v1
curl -I http://localhost:8087
```

Open: `http://YOUR_EC2_PUBLIC_IP:8087`

### Project 8 — Student Management (8088)

A demo student dashboard. Use fictional student records only.

```bash
cd ~/Docker-and-Kubernetes/vyomanant-devops-projects/08-student-management
sudo docker build -t vyomanant-students:v1 .
sudo docker run -d --name student-management --restart unless-stopped -p 8088:80 vyomanant-students:v1
curl -I http://localhost:8088
```

Open: `http://YOUR_EC2_PUBLIC_IP:8088`

### Project 9 — DevOps Demo Portal (8090)

A central landing page linking to the other eight deployed apps. **Deploy the other apps first** so its links have working destinations.

```bash
cd ~/Docker-and-Kubernetes/vyomanant-devops-projects/09-devops-demo-portal
sudo docker build -t vyomanant-demo-portal:v1 .
sudo docker run -d --name vyomanant-demo-portal --restart unless-stopped -p 8090:80 vyomanant-demo-portal:v1
curl -I http://localhost:8090
```

Open: `http://YOUR_EC2_PUBLIC_IP:8090`

**Why the portal links work:** It uses the current hostname with the individual application ports. All nine containers must be running and reachable from the browser. The portal itself does not deploy or monitor the other apps.

## 7. Verify and troubleshoot

### List all running containers

```bash
sudo docker ps
sudo docker ps -a
sudo docker images
```

You should see nine running containers after completing all nine projects.

### Check application logs and NGINX

```bash
sudo docker logs --tail 50 calculator-app
sudo docker exec calculator-app nginx -t
sudo docker inspect --format='{{.State.Health.Status}}' calculator-app
```

**Note:** Health status is meaningful only when the image defines a Docker `HEALTHCHECK`. Otherwise Docker may show no health status.

### Test all HTTP endpoints locally

```bash
for port in 8081 8082 8083 8084 8085 8086 8087 8088 8090; do
  echo "Testing port $port"
  curl --max-time 5 -s -o /dev/null -w 'HTTP %{http_code}\n' "http://localhost:$port/"
done
```

An `HTTP 200` is expected for a working root page. This confirms the server responds; test application interactions separately in a browser.

### Common problems

| Symptom | Likely cause | What to do |
|---|---|---|
| `permission denied` talking to Docker | Docker socket access | Use `sudo docker ...` |
| `port is already allocated` | Another process/container uses that host port | Run `sudo docker ps` and `sudo ss -ltnp`; stop the conflicting service or change the host port |
| `container name is already in use` | A container with that name already exists | Stop/remove it before running the same name again |
| Local `curl` works but browser times out | EC2 security group, network ACL, firewall, or wrong IP | Verify inbound port rule from your IP and correct public IP |
| `curl` connection refused | Container not running or port mapping wrong | Check `sudo docker ps -a` and `sudo docker logs NAME` |
| Browser displays old content | Old image/container or cached browser page | Rebuild, recreate container, then hard-refresh browser |
| Portal loads but app links fail | Other apps not running or ports blocked | Deploy the eight apps and verify their ports |

### Port already in use?

```bash
sudo ss -ltnp | grep ':8081'
sudo docker ps --format 'table {{.Names}}\t{{.Ports}}'
```

Do not remove unrelated containers or services without confirming their purpose.

## 8. Stop, restart, and delete containers

Example with the calculator:

```bash
sudo docker stop calculator-app        # stop without deleting
sudo docker start calculator-app       # start again
sudo docker restart calculator-app     # restart
sudo docker logs -f calculator-app     # follow logs (Ctrl+C to exit)
```

To delete a container **without deleting its image**:

```bash
sudo docker stop calculator-app
sudo docker rm calculator-app
```

To remove its image (only when no container depends on it):

```bash
sudo docker image rm vyomanant-calculator:v1
```

**Stop all nine project containers** (only those listed here):

```bash
sudo docker stop calculator-app finance-app food-app games-app ecommerce-app task-manager banking-app student-management vyomanant-demo-portal
```

**Start them again:**

```bash
sudo docker start calculator-app finance-app food-app games-app ecommerce-app task-manager banking-app student-management vyomanant-demo-portal
```

> The `--restart unless-stopped` option automatically restarts containers after Docker/EC2 restarts unless you explicitly stopped them.

## 9. Pull changes and redeploy

If source code changes are committed to GitHub, update the EC2 checkout:

```bash
cd ~/Docker-and-Kubernetes
git pull origin main
```

A `git pull` **does not update a running container**. Rebuild and recreate the changed application. Example for Calculator:

```bash
cd ~/Docker-and-Kubernetes/vyomanant-devops-projects/01-smart-calculator
sudo docker build -t vyomanant-calculator:v1 .
sudo docker stop calculator-app
sudo docker rm calculator-app
sudo docker run -d --name calculator-app --restart unless-stopped -p 8081:80 vyomanant-calculator:v1
curl -I http://localhost:8081
```

**Explanation:** A Docker image is a built snapshot. Updating files on EC2 does not change an existing image or container; rebuild and recreate to deploy changes.

## 10. Classroom exercises

1. **Git:** Clone the repository, inspect folders, and view the current branch with `git branch`.
2. **Docker build:** Build the calculator image; explain `Dockerfile`, build context, image tag, and image layers.
3. **Docker run:** Run Calculator on 8081; explain detached mode, container name, restart policy, and port mapping.
4. **Isolation:** Deploy Finance Manager on 8082; show two independent containers with `docker ps`.
5. **NGINX:** Run `nginx -t` inside a container and inspect `nginx.conf`.
6. **Troubleshooting:** Intentionally try to reuse an occupied host port; explain the conflict and resolve it.
7. **Logs and health:** Inspect logs and health check results where supported.
8. **Persistence:** Change browser-local data, refresh, and compare with a second browser/device. Explain why this is not server-side persistence.
9. **Updates:** Change visible HTML text, rebuild the image, recreate the container, and verify the change.
10. **Portal:** Deploy all eight apps and the central portal; verify each link and discuss AWS security groups.

### Expected outcome

Students can independently **clone source code → build Docker images → run containers → test HTTP → inspect logs → troubleshoot → update deployments**. This foundation can later be extended with **Docker Compose, CI/CD, ECR, Kubernetes, EKS, Terraform, and monitoring**.

---

**Vyomanant Academy** · **Learn. Build. Grow.**  
https://www.vyomanant.com · academy@vyomanant.com
