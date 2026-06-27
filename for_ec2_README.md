# 🚀 Kubernetes Rolling Update Project

A hands-on DevOps project demonstrating **Kubernetes Rolling Updates** and **Rollback** using Docker, Minikube, and AWS EC2.

---

## 📁 Project Structure

```
kubernetes-rollback-project/
├── indexv1.html        # Version 1 — Stable release
├── indexv2.html        # Version 2 — Broken release (CrashLoopBackOff simulation)
├── indexv3.html        # Version 3 — Hotfix recovered release
├── Dockerfilev1        # Dockerfile for v1
├── Dockerfilev2        # Dockerfile for v2
├── Dockerfilev3        # Dockerfile for v3
├── configmap.yaml      # Kubernetes ConfigMap
├── deployment.yaml     # Kubernetes Deployment (RollingUpdate strategy)
├── service.yaml        # Kubernetes NodePort Service
├── web.yaml            # Combined manifest (single-apply file)
└── README.md           # Project documentation
```

---

## 🛠️ Tools Used

| Tool | Purpose |
|---|---|
| Docker | Build and containerize app images |
| Docker Hub | Store and pull container images |
| Kubernetes (kubectl) | Deploy and manage containers |
| Minikube | Local Kubernetes cluster |
| AWS EC2 | Cloud server to run the project |
| Git & GitHub | Version control |

---

## ⚙️ EC2 Setup — Install Required Services

### Step 1 — System Update
```bash
sudo apt update && sudo apt upgrade -y
```

### Step 2 — Install Docker
```bash
sudo apt install docker.io -y
sudo systemctl start docker
sudo systemctl enable docker
sudo usermod -aG docker $USER && newgrp docker
docker --version
```

### Step 3 — Install kubectl
```bash
sudo apt install -y apt-transport-https ca-certificates curl

curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.29/deb/Release.key | \
  sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg

echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] \
  https://pkgs.k8s.io/core:/stable:/v1.29/deb/ /' | \
  sudo tee /etc/apt/sources.list.d/kubernetes.list

sudo apt update && sudo apt install -y kubectl
kubectl version --client
```

### Step 4 — Install Minikube
```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
minikube start --driver=docker
minikube status
```

### Step 5 — Install Git & Clone Project
```bash
sudo apt install git -y
git clone https://github.com/yourusername/kubernetes-rollback-project.git
cd kubernetes-rollback-project
```

---

## 🐳 Build & Push Docker Images

```bash
# Build all 3 versions
docker build -f Dockerfilev1 -t manireet0607/repoforec2:v1 .
docker build -f Dockerfilev2 -t manireet0607/repoforec2:v2 .
docker build -f Dockerfilev3 -t manireet0607/repoforec2:v3 .

# Login to Docker Hub
docker login

# Push all 3 versions
docker push manireet0607/repoforec2:v1
docker push manireet0607/repoforec2:v2
docker push manireet0607/repoforec2:v3
```

---

## ☸️ Deploy to Kubernetes

```bash
# Apply all manifests
kubectl apply -f configmap.yaml
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml

# Verify everything is running
kubectl get pods
kubectl get deployments
kubectl get services
```

---

## 🌐 Access the App

### Local / Docker Desktop
```bash
kubectl port-forward service/web-service 8080:80
# Open: http://localhost:8080
```

### AWS EC2
```bash
kubectl port-forward service/web-service 8080:80 --address 0.0.0.0
# Open: http://<your-ec2-public-ip>:8080
```

> ⚠️ Make sure port **8080** is open in your EC2 Security Group inbound rules!

---

## 🔄 Rolling Updates

```bash
# Update to v2 (broken release)
kubectl set image deployment/web-deployment web=manireet0607/repoforec2:v2
kubectl rollout status deployment/web-deployment

# Update to v3 (hotfix release)
kubectl set image deployment/web-deployment web=manireet0607/repoforec2:v3
kubectl rollout status deployment/web-deployment
```

---

## ⏪ Rollback

```bash
# Rollback one step back
kubectl rollout undo deployment/web-deployment

# Rollback to a specific version
kubectl rollout undo deployment/web-deployment --to-revision=1

# View rollout history
kubectl rollout history deployment/web-deployment
```

---

## 🐛 Common Errors & Fixes

| Error | Cause | Fix |
|---|---|---|
| `docker driver should not be used with root` | Running minikube as root | Use `ubuntu` user, not root |
| `ImagePullBackOff` | Wrong image name in deployment.yaml | Fix image name with `sed` or edit manually |
| `ErrImagePull` | Not logged into Docker Hub | Run `docker login` first |
| `connection refused` | Minikube not running | Run `minikube start --driver=docker` |
| `no space left on device` | EC2 disk full | Run `docker system prune -a --volumes -f` |
| `unmarshal: unexpected end of JSON` | Corrupted Minikube config | Run `minikube delete --all --purge && rm -rf ~/.minikube` |
| `ERR_CONNECTION_TIMED_OUT` | Port blocked by AWS | Open port 8080 in EC2 Security Group |
| `cannot assign requested address` | Wrong address in port-forward | Use `--address 0.0.0.0` not public IP |

---

## 🔁 Deployment Flow

```
v1 (Stable) → v2 (Broken) → Rollback → v3 (Hotfix)
     ✅             ❌           ⏪           🛡️
```

---

## ☁️ EC2 Instance Details

| Setting | Value |
|---|---|
| OS | Ubuntu 22.04 LTS |
| Instance Type | t2.medium (minimum) |
| Storage | 20GB |
| Ports Open | 22, 8080 |
| Driver | Minikube with Docker driver |

---

## 📌 Key Learnings

- How Kubernetes **Rolling Updates** work with zero downtime
- How to **rollback** to a previous version instantly
- Debugging real-world issues like `ImagePullBackOff`, `CrashLoopBackOff`
- Running Kubernetes on **AWS EC2** in production-like environment
- Docker image build, tag, and push workflow
- AWS **Security Group** configuration for port access

---

## 👤 Author

**Manireet** — [@manireet0607](https://hub.docker.com/u/manireet0607)

#Kubernetes #Docker #DevOps #AWS #EC2 #CloudNative #K8s
