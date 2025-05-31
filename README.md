# Kubernetes-HandsOn

---

## ✅ Step-by-Step Fix: Install `kubectl` Binary on Ubuntu 24.04

### 🔹 Step 1: Manually get the latest stable version string

Run this to check if version retrieval is working:

```bash
curl -s https://dl.k8s.io/release/stable.txt
```

You should see something like:

```
v1.30.1
```

If this doesn't work or returns nothing, you may have networking issues or `curl` is outdated. In that case, skip to **manual version pinning** in Step 2.

---

### 🔹 Step 2: Download a known version manually (e.g., v1.30.1)

```bash
curl -LO https://dl.k8s.io/release/v1.30.1/bin/linux/amd64/kubectl
```

### 🔹 Step 3: Make it executable

```bash
chmod +x kubectl
```

### 🔹 Step 4: Move to a directory in your `$PATH`

```bash
sudo mv kubectl /usr/local/bin/
```

### 🔹 Step 5: Verify installation

```bash
kubectl version --client
```

---

Great! Let's continue by **installing Minikube**, which creates a local Kubernetes cluster ideal for testing, learning, and development.

---

## 🚀 Step-by-Step: Install and Start Minikube on Ubuntu 24.04

### ✅ **Step 1: Install Dependencies**

```bash
sudo apt-get update
sudo apt-get install -y curl wget apt-transport-https ca-certificates gnupg
```

### ✅ **Step 2: Install Minikube Binary**

```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

### ✅ **Step 3: Verify Minikube Installation**

```bash
minikube version
```

You should see output like `minikube version: v...`

---

## 💻 Step 4: Start Minikube with Docker (Recommended on EC2/VM)

If you have Docker installed, use:

```bash
minikube start --driver=docker
```

If Docker is not installed yet:

```bash
sudo apt-get install -y docker.io
sudo usermod -aG docker $USER
newgrp docker
```

Then restart Minikube:

```bash
minikube start --driver=docker
```

---

### ⚙️ Alternative: Start Minikube with `none` driver (no VM, for root user)

This runs Minikube directly on the host OS (advanced, root-only).

```bash
minikube start --driver=none
```

> ⚠️ Must be run as `root`, and `kubelet` may require configuration fixes. **Prefer Docker driver** if possible.

---

## ✅ Step 5: Verify Kubernetes Cluster is Running

```bash
kubectl get nodes
kubectl cluster-info
```

---

## 🔍 Step 6: Deploy a Test App

```bash
kubectl create deployment hello-minikube --image=kicbase/echo-server:1.0
kubectl expose deployment hello-minikube --type=NodePort --port=8080
minikube service hello-minikube
```

> This will open the service in a browser (on GUI desktop) or return a URL (use `curl` on a headless server).

---

## 🧹 Step 7: Clean Up

```bash
kubectl delete service hello-minikube
kubectl delete deployment hello-minikube
minikube stop
```

---

### ✅ Want to Access Kubernetes Dashboard?

```bash
minikube dashboard
```

(For headless: use `minikube dashboard --url` and open the link in your browser.)

---

Let me know if you'd like to:

* Proceed with **Helm installation**
* Add **Ingress**, **Volumes**, or **Secrets**
* Deploy a **real-world app** like WordPress or Spring Boot



