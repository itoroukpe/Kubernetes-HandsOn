**complete step-by-step guide for DevOps students** on how to **create an Amazon EKS (Elastic Kubernetes Service) cluster**
---

# ☁️ **Amazon EKS Setup Guide (Step-by-Step with Explanations)**

## 🧑‍🏫 Target Audience:

* DevOps students & practitioners
* Learning goal: Deploy and manage Kubernetes on AWS using EKS

---

## ✅ Step 1: Create an AWS Account

### 1. Go to [https://aws.amazon.com](https://aws.amazon.com)

* Click **Create an AWS Account**
* Enter your **email, password, and account name**
* Provide **billing details** (free tier available)
* Complete **identity verification**
* Choose the **Basic Support Plan (free)**

🔐 **Note**: Store your AWS credentials safely.

---

## ✅ Step 2: Set Up IAM User for CLI Access

### 1. Sign in to AWS Console as the root user

### 2. Go to **IAM (Identity & Access Management)**:

* Create a new **IAM user** (e.g., `eks-admin`)
* Enable **Programmatic access**
* Attach **AdministratorAccess** policy
* Download your **Access Key ID and Secret Access Key**

---

## ✅ Step 3: Configure AWS CLI on Your Machine

### 1. Install AWS CLI

```bash
# macOS
brew install awscli

# Ubuntu
sudo apt-get install awscli -y
```

### 2. Configure your CLI credentials

```bash
aws configure
```

> You will be prompted to enter:

* Access Key ID
* Secret Access Key
* Region (e.g., `us-west-2`)
* Output format (use `json`)

---

## ✅ Step 4: Install Required Tools

Install these tools on your local machine or DevOps VM:

```bash
# eksctl (EKS cluster management tool)
curl -LO "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz"
tar -xzf eksctl_*.tar.gz
sudo mv eksctl /usr/local/bin/

# kubectl (Kubernetes CLI)
curl -LO "https://dl.k8s.io/release/v1.30.1/bin/linux/amd64/kubectl"
chmod +x kubectl
sudo mv kubectl /usr/local/bin/
```

Check versions:

```bash
eksctl version
kubectl version --client
```

---

## ✅ Step 5: Create Your EKS Cluster

### ⚙️ Create EKS cluster with `eksctl`

```bash
eksctl create cluster \
  --name rondustech-eks \
  --region us-west-2 \
  --nodes 2 \
  --node-type t3.medium \
  --with-oidc \
  --managed
```

🕐 Takes \~15 minutes to complete. This creates:

* VPC
* Subnets
* Node group
* Control plane

---

## ✅ Step 6: Verify EKS Setup

```bash
kubectl get nodes
```

You should see your EC2 worker nodes in `Ready` state.

---

## ✅ Step 7: Deploy an App to EKS

### 1. Create a deployment

```bash
kubectl create deployment nginx --image=nginx
```

### 2. Expose the deployment

```bash
kubectl expose deployment nginx --port=80 --type=LoadBalancer
```

### 3. Get the external IP

```bash
kubectl get svc nginx
```

Look under `EXTERNAL-IP` — wait until it shows a value, then visit it in a browser.

---

## ✅ Step 8: Clean Up Resources (Optional)

```bash
eksctl delete cluster --name rondustech-eks --region us-west-2
```

---

## 📘 Summary Table

| Task               | Command/Action              |
| ------------------ | --------------------------- |
| Create AWS account | aws.amazon.com              |
| Configure IAM      | IAM → New User              |
| Set up AWS CLI     | `aws configure`             |
| Create EKS Cluster | `eksctl create cluster`     |
| Check Nodes        | `kubectl get nodes`         |
| Deploy App         | `kubectl create deployment` |
| Expose App         | `kubectl expose deployment` |
| Clean Up           | `eksctl delete cluster`     |

---

**step-by-step guide to deploy a real-world app (WAR file)** on **Amazon EKS**, using the Docker image from Docker Hub:
📦 `rondustech/scientific-calculator.war`

We'll assume the image runs on **Tomcat** or similar and listens on port **8080**.

---

## 🎯 Objective

* Deploy `rondustech/scientific-calculator.war` on EKS
* Use a LoadBalancer service to expose it
* Verify that the app is accessible via public URL

---

## ✅ Prerequisites

Before starting, make sure you:

1. Have an active AWS account
2. Have EKS created and `kubectl` connected
3. Docker image is available at Docker Hub
4. You’ve tested `kubectl get nodes` successfully

---

## 📁 Step 1: Create Kubernetes YAML Manifests

Create a folder called `eks-app/` and inside it, add the following files:

---

### 🧱 1. `deployment.yaml`

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: scientific-calculator
spec:
  replicas: 1
  selector:
    matchLabels:
      app: calculator
  template:
    metadata:
      labels:
        app: calculator
    spec:
      containers:
        - name: calculator
          image: rondustech/scientific-calculator.war
          ports:
            - containerPort: 8080
```

---

### 🌐 2. `service.yaml`

```yaml
apiVersion: v1
kind: Service
metadata:
  name: calculator-service
spec:
  type: LoadBalancer
  selector:
    app: calculator
  ports:
    - port: 80
      targetPort: 8080
      protocol: TCP
```

---

## 🚀 Step 2: Deploy to EKS

Navigate into your folder and apply the manifests:

```bash
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
```

---

## 🧪 Step 3: Verify Deployment

### 1. Check pod and deployment status:

```bash
kubectl get pods
kubectl get deployments
```

> Make sure the pod status is `Running`.

---

### 2. Get the service external IP:

```bash
kubectl get svc calculator-service
```

Output should look like:

```
NAME                TYPE           CLUSTER-IP     EXTERNAL-IP       PORT(S)        AGE
calculator-service  LoadBalancer   10.0.123.45    3.22.45.89         80:30500/TCP   3m
```

Visit `http://<EXTERNAL-IP>` in your browser.

---

## 🧹 Optional: Clean Up

```bash
kubectl delete -f deployment.yaml
kubectl delete -f service.yaml
```

---

## 📝 Optional Enhancements

| Feature           | How to Add                                |
| ----------------- | ----------------------------------------- |
| HTTPS/TLS         | Use Ingress + Cert-Manager                |
| DNS with Route 53 | Point domain to LoadBalancer              |
| Auto-scaling      | Add HPA config                            |
| CI/CD             | Use GitHub Actions to auto-deploy on push |

---



