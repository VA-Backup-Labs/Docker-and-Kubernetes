# Vyomanant Academy — DevOps Demo Projects

Nine standalone HTML/CSS/JavaScript applications, containerized with NGINX Alpine for Docker and AWS EC2.

> Educational demonstrations. The banking and checkout features are simulations; browser data is local to each device.

| Project | Host port |
|---|---:|
| `01-smart-calculator` | 8081 |
| `02-finance-manager` | 8082 |
| `03-food-delivery` | 8083 |
| `04-games-arcade` | 8084 |
| `05-ecommerce-store` | 8085 |
| `06-task-manager` | 8086 |
| `07-banking-simulator` | 8087 |
| `08-student-management` | 8088 |
| `09-devops-demo-portal` | 8090 |

## Run an application

From the application directory:

```bash
docker build -t vyomanant-demo:local .
docker run -d --name vyomanant-demo --restart unless-stopped -p 8081:80 vyomanant-demo:local
```

Change host port `8081` to the value listed for the selected application. Use unique container names for concurrent deployments.

## Push to GitHub

Create an empty GitHub repository named `vyomanant-devops-projects`, then from this folder run:

```bash
git init
git add .
git commit -m "Add nine Vyomanant Academy demo applications"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/vyomanant-devops-projects.git
git push -u origin main
```

Replace `YOUR_USERNAME`. Use GitHub-supported authentication.
