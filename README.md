# SimpleDevOps

A minimalist, beginner-friendly DevOps guide and web project demonstrating how to host a static website online using Git, Docker, AWS EC2, and GitHub Actions (building directly on EC2 for simplicity).

---

## Architecture Overview

```text
[Developer Laptop]
       │
       │  git push origin main
       ▼
[GitHub Repository]
       │
       │  Triggers Workflow
       ▼
[GitHub Actions Runner]
       │
       │  SSH Connection (secrets.EC2_HOST, EC2_SSH_KEY)
       ▼
[AWS EC2 Instance (Ubuntu 24.04)]
       │
       ├── 1. git pull origin main
       ├── 2. docker build -t simple-devops-app .
       └── 3. docker run -d -p 80:80 simple-devops-app
              │
              ▼
    [Public Internet / Port 80]
```

---

## Quick Start (Step-by-Step)

### 1. Initialize Local Git Repository

```bash
cd /Users/satyamrana/Desktop/SimpleDevOps
git init
git add .
git commit -m "Initial commit of SimpleDevOps project"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/SimpleDevOps.git
git push -u origin main
```

---

### 2. Launch AWS EC2 Instance

1. Open AWS Console > **EC2** > **Launch Instance**.
2. Select **Ubuntu 24.04 LTS** and instance type **t2.micro** (Free Tier eligible).
3. Create a Key Pair named `simple-devops-key.pem` and download it.
4. Under Network Settings, configure your **Security Group** with:
   - **SSH (Port 22)**: Allowed from Anywhere (`0.0.0.0/0`) or My IP.
   - **HTTP (Port 80)**: Allowed from Anywhere (`0.0.0.0/0`).
5. Launch the instance and note its **Public IPv4 address**.

---

### 3. One-Time Setup on EC2

SSH into your server and install Docker and Git:

```bash
chmod 400 simple-devops-key.pem
ssh -i simple-devops-key.pem ubuntu@YOUR_EC2_PUBLIC_IP

# On the EC2 server:
sudo apt update -y
sudo apt install -y docker.io git
sudo usermod -aG docker ubuntu
newgrp docker
docker --version
exit
```

---

### 4. Configure GitHub Repository Secrets

In your GitHub repository:
1. Navigate to **Settings** > **Secrets and variables** > **Actions**.
2. Click **New repository secret** and add:
   - `EC2_HOST`: Your EC2 public IP (e.g. `13.233.12.34`)
   - `EC2_USER`: `ubuntu`
   - `EC2_SSH_KEY`: The entire content of your `simple-devops-key.pem` file (including header and footer lines).

---

### 5. Automated Deployment

Any time you push to the `main` branch:

```bash
git add .
git commit -m "Update website content"
git push origin main
```

GitHub Actions will automatically SSH into your EC2 server, pull the latest code, build the Docker image on the server, and launch the updated container on Port 80.

---

## File Structure

```text
SimpleDevOps/
├── .github/
│   └── workflows/
│       └── deploy.yml    # GitHub Actions automation script
├── Dockerfile            # Lightweight Nginx container definition
├── index.html            # Documentation page and live project
├── styles.css            # Apple-inspired minimalist design system
├── script.js             # Interactive copy buttons & active navigation
└── README.md             # Project handbook
```
