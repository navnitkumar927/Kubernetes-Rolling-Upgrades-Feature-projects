# Kubernetes Rolling Update Practice — v2

## Project Structure
- indexv1.html — Stable release (v1.0.0)
- indexv2.html — Broken release with CrashLoopBackOff simulation (v2.0.0)
- indexv3.html — Hotfix recovered release (v3.0.0)
- Dockerfilev1/v2/v3 — One Dockerfile per version
- configmap.yaml — Kubernetes ConfigMap
- deployment.yaml — Rolling update deployment
- service.yaml — NodePort service
- web.yaml — Combined manifest

## Quick Start

### 1. Build & Push Images
docker build -f Dockerfilev1 -t manireet0607/manireet:v1 .
docker build -f Dockerfilev2 -t manireet0607/manireet:v2 .
docker build -f Dockerfilev3 -t manireet0607/manireet:v3 .
docker push manireet0607/manireet:v1
docker push manireet0607/manireet:v2
docker push manireet0607/manireet:v3

### 2. Deploy
kubectl apply -f configmap.yaml
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml

### 3. Access
kubectl port-forward service/web-service 8080:80
# Open http://localhost:8080

### 4. Rolling Updates
kubectl set image deployment/web-deployment web=manireet0607/manireet:v2
kubectl set image deployment/web-deployment web=manireet0607/manireet:v3

### 5. Rollback
kubectl rollout undo deployment/web-deployment
kubectl rollout undo deployment/web-deployment --to-revision=1
