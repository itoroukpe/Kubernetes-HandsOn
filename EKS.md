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
Here are the **step-by-step instructions** to:

✅ **Create a new IAM user (e.g., `eks-admin`)**
✅ **Enable Programmatic Access**
✅ **Attach `AdministratorAccess` policy**

---

### 🔐 Step 1: Log in to AWS Console

1. Go to [https://console.aws.amazon.com/iam](https://console.aws.amazon.com/iam)
2. Make sure you are in the correct **AWS account and region**

---

### 👤 Step 2: Create a New IAM User

1. In the left sidebar, click **Users**
2. Click the **“Add users”** button
3. Under **User name**, enter: `eks-admin`
4. Under **Select AWS credential type**:

   * ✅ Check **Programmatic access** (for CLI/SDK access)
   * ❌ You may uncheck **Console access** unless you also want web UI login
5. Click **Next: Permissions**

---

### 🔐 Step 3: Set Permissions

1. Choose **“Attach policies directly”**
2. In the search box, type: `AdministratorAccess`
3. Check the box next to **AdministratorAccess** (this grants full permissions)

> 🔒 **Caution**: This gives full access to all AWS services. Use with trusted users only.

4. Click **Next: Tags** (optional, you can skip or add tags)
5. Click **Next: Review**

---

### ✅ Step 4: Review and Create User

1. Confirm the user name is `eks-admin`
2. Confirm **Programmatic access** is enabled
3. Confirm `AdministratorAccess` policy is attached
4. Click **Create user**

---

### 📄 Step 5: Save Access Credentials

1. On the success page, you will see:

   * **Access key ID**
   * **Secret access key**

> ⚠️ Make sure to **download the .csv file** or copy these credentials immediately. You **will not see the secret again**.

---
## ✅ Step 3: Configure AWS CLI on Your Machine

### 1. Install AWS CLI

```bash
# macOS
brew install awscli

# Ubuntu
sudo apt-get install awscli -y
```
You're seeing this error because the `awscli` package is not available from the default Ubuntu repositories in your current configuration. Here's how to correctly install the **latest AWS CLI v2** on Ubuntu:

---

### ✅ **Steps to Install AWS CLI v2 on Ubuntu**

1. **Update your system packages**

```bash
sudo apt update && sudo apt upgrade -y
```

2. **Install required dependencies**

```bash
sudo apt install unzip curl -y
```

3. **Download the AWS CLI v2 installer**

```bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
```

4. **Unzip the installer**

```bash
unzip awscliv2.zip
```

5. **Run the install script**

```bash
sudo ./aws/install
```

6. **Verify the installation**

```bash
aws --version
```

You should see something like:

```bash
aws-cli/2.x.x Python/3.x.x Linux/x86_64 source
```

---



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



