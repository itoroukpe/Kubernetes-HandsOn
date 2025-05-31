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



---
If `curl` to the Minikube service IP works but **the browser doesn't show anything**, there are a few common reasons — especially when running on a **cloud VM like AWS EC2** or on a **headless server**.

---

## 🕵️‍♂️ Likely Reasons & Fixes

### 1. **Minikube IP (`192.168.49.2`) is Local-Only**

* That IP is **Minikube’s internal virtual network**, **not accessible** from outside your VM (e.g., from your laptop browser).
* `curl` works because you're inside the VM — the **browser on your local machine cannot reach it**.

#### ✅ **Fix**: Use `minikube service` to tunnel a route

On your VM:

```bash
minikube service hello-minikube --url
```

It will return something like:

```
http://127.0.0.1:53147
```

Then:

* On your **local machine**, **SSH tunnel** to your EC2 VM:

```bash
ssh -i your-key.pem -L 53147:127.0.0.1:53147 ubuntu@<your-ec2-public-ip>
```

* Now, open your browser and go to:

```
http://localhost:53147
```

You should see the response!

---

### 2. **Security Group / Firewall is Blocking Port**

If you try to access `http://<EC2-PUBLIC-IP>:<NodePort>` directly:

* EC2's security group **must allow inbound traffic** on that NodePort (e.g., `31792`)
* Check with:

```bash
kubectl get svc hello-minikube
```

#### ✅ Fix:

* Go to your AWS EC2 dashboard
* Edit the **Security Group** attached to your VM
* Add an **inbound rule**:

  * Type: Custom TCP
  * Port range: `30000-32767` (NodePort range)
  * Source: Your IP or `0.0.0.0/0` (public for testing)

Now you can visit:

```
http://<EC2-PUBLIC-IP>:31792
```

---

### 3. **App Only Returns Plain Text**

Your app (`kicbase/echo-server`) just responds with:

```
Request served by <pod-name>
```

– which looks like plain text in browser and may not “look” like a website.

---

## ✅ Summary Fix Steps:

| Problem                             | Solution                                                      |
| ----------------------------------- | ------------------------------------------------------------- |
| Internal Minikube IP not accessible | Use `minikube service hello-minikube --url` and SSH tunnel    |
| EC2 port blocked                    | Allow NodePort range `30000-32767` in Security Group          |
| App is not HTML                     | Use a different container (like `nginx`) to serve a real page |

---

### ✅ Bonus: Try a Real Web App

```bash
kubectl create deployment web --image=nginx
kubectl expose deployment web --type=NodePort --port=80
minikube service web
```

This will give you a browser-friendly HTML page.





