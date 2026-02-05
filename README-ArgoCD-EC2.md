# 🚀 Argo CD Installation on EC2 (Ubuntu)

This guide explains how to install **Argo CD** on an **EC2 instance** using **Kubernetes (Minikube)**.

---

## 📌 Prerequisites

Before starting, make sure you have:

- EC2 instance (Ubuntu 20.04+)
- SSH access to the instance
- User with sudo privileges
- At least **2 vCPU / 4 GB RAM** (recommended)
- Open ports:
  - 22 (SSH)
  - 80 / 443
  - 8080 (Argo CD UI)

---

## 🔹 Step 1: Update System

```bash
sudo apt update && sudo apt upgrade -y
```

---

## 🔹 Step 2: Install Docker

```bash
sudo apt install -y docker.io
sudo systemctl start docker
sudo systemctl enable docker
sudo usermod -aG docker $USER
newgrp docker
```

Verify:
```bash
docker --version
```

---

## 🔹 Step 3: Install kubectl

```bash
curl -LO https://dl.k8s.io/release/$(curl -Ls https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl
chmod +x kubectl
sudo mv kubectl /usr/local/bin/
```

Verify:
```bash
kubectl version --client
```

---

## 🔹 Step 4: Install Minikube

```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
chmod +x minikube-linux-amd64
sudo mv minikube-linux-amd64 /usr/local/bin/minikube
```

Start Minikube:
```bash
minikube start --driver=docker
```

Verify:
```bash
kubectl get nodes
```

---

## 🔹 Step 5: Install Argo CD

Create Namespace:
```bash
kubectl create namespace argocd
```

Apply Argo CD Manifest:
```bash
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

Check Pods:
```bash
kubectl get pods -n argocd
```

---

## 🔹 Step 6: Expose Argo CD Server

### Option 1: Port Forwarding

```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

Access:
```
https://<EC2_PUBLIC_IP>:8080
```

---

### Option 2: NodePort

```bash
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "NodePort"}}'
kubectl get svc -n argocd
```

Access:
```
https://<EC2_PUBLIC_IP>:<NODE_PORT>
```

---

## 🔹 Step 7: Get Admin Password

```bash
kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath="{.data.password}" | base64 --decode
```

Username: admin  
Password: output of the command

---

## 🔹 Step 8: Install Argo CD CLI (Optional)

```bash
curl -sSL -o argocd https://github.com/argoproj/argo-cd/releases/latest/download/argocd-linux-amd64
chmod +x argocd
sudo mv argocd /usr/local/bin/
```

Verify:
```bash
argocd version
```

---

## 🧪 Verification

```bash
kubectl get all -n argocd
```

---

## 📚 References

- https://argo-cd.readthedocs.io
- https://kubernetes.io
