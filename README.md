
---

# 🚀 DevOps CI/CD Pipeline – GitHub → Jenkins → Docker → Docker Hub → Auto Deployment

This project demonstrates a complete **CI/CD pipeline** built using industry‑standard DevOps tools.  
Whenever code is pushed to GitHub, Jenkins automatically builds, tests, containerizes, pushes the image to Docker Hub, and deploys the updated application on the server.

---

## 📌 **Project Overview**

This is a fully automated CI/CD pipeline that includes:

- **GitHub** for version control  
- **GitHub Webhooks** for automatic Jenkins triggers  
- **Jenkins Declarative Pipeline** for CI/CD  
- **Docker** for containerization  
- **Docker Hub** for image registry  
- **Automated Deployment** on server  
- **ngrok** for secure public webhook tunneling  

This pipeline ensures **zero‑downtime deployment** and **automatic updates** on every code push.

---

## 🧩 **Pipeline Architecture**

```
Developer → GitHub → Webhook → Jenkins → Docker Build → Docker Hub → Server → Auto Deploy
```

---

## ⚙️ **How the Pipeline Works**

### 1️⃣ **Developer pushes code to GitHub**
Any update in the repository triggers the webhook.

### 2️⃣ **GitHub Webhook → Jenkins**
Webhook sends a POST request to Jenkins via ngrok public URL.

### 3️⃣ **Jenkins Pipeline Stages**

#### ✔ Checkout Code
Pulls the latest code from GitHub.

#### ✔ Build Docker Image
```
docker build -t aashikali240/devops-demo:latest .
```

#### ✔ Push Image to Docker Hub
```
docker push aashikali240/devops-demo:latest
```

#### ✔ Deploy Updated Container
```
docker pull aashikali240/devops-demo:latest
docker rm -f devops-app || true
docker run -d --name devops-app -p 8081:80 aashikali240/devops-demo:latest
```

### 4️⃣ **Application Auto‑Updates**
No manual restart required.  
Browser instantly shows the latest version.

---

## 🛠 **Technologies Used**

| Tool | Purpose |
|------|---------|
| **GitHub** | Source code management |
| **Jenkins** | CI/CD automation |
| **Docker** | Containerization |
| **Docker Hub** | Image registry |
| **ngrok** | Public webhook tunneling |
| **Linux (Ubuntu)** | Deployment environment |

---

## 📂 **Project Structure**

```
devops-project/
│── app/
│   └── index.html
│── Dockerfile
│── Jenkinsfile
│── docker-compose.yml
│── README.md
```

---

## 🧠 **Key DevOps Skills Demonstrated**

- CI/CD pipeline creation  
- Jenkins Declarative Pipeline  
- GitHub Webhooks configuration  
- Docker image build & push  
- Automated deployment  
- Container lifecycle management  
- Real‑time application updates  
- Production‑style workflow  
- Linux server management  

---

## 📸 **Live Demo (Local Deployment)**

Application runs on:

```
http://<server-ip>:8081/
```

---

## 🎯 **Why This Project Is Job‑Ready**

This project demonstrates real DevOps capabilities required in Saudi Arabia:

- Automation  
- CI/CD  
- Docker  
- Jenkins  
- Webhooks  
- Deployment  
- Linux  
- Troubleshooting  

This is a **complete end‑to‑end DevOps pipeline**, suitable for interviews and portfolio.

---

## 👤 **Author**

**Aashik Ali**  
DevOps Engineer | Docker | Jenkins | CI/CD | Linux  
Saudi Arabia  

---

