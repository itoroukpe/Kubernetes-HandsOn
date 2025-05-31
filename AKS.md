**complete guide for DevOps students** 

1. ✅ **Create an Azure Kubernetes Service (AKS)** cluster
2. ✅ **Deploy a real-world app** using the image `rondustech/scientific-calculator.war` from Docker Hub

---

# ☁️ Step-by-Step Guide: **Deploying to Azure AKS**

---

## Part 1️⃣: Create an Azure Account and AKS Cluster

---

## ✅ Step 1: Create an Azure Account

1. Go to: [https://azure.microsoft.com](https://azure.microsoft.com)
2. Click **"Start free"**
3. Sign up with your email, verify identity, and set billing info
4. You'll receive **\$200 free credit** (at the time of writing)

---

## ✅ Step 2: Install Azure CLI and Kubernetes CLI

### For Ubuntu/macOS:

```bash
# Azure CLI
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash

# kubectl
az aks install-cli
```

Verify:

```bash
az version
kubectl version --client
```

---

## ✅ Step 3: Login and Create Resource Group

```bash
az login
az account set --subscription "<your-subscription-id>"  # Optional if multiple subscriptions
az group create --name rondustech-rg --location eastus
```

---

## ✅ Step 4: Create AKS Cluster

```bash
az aks create \
  --resource-group rondustech-rg \
  --name rondustech-aks \
  --node-count 2 \
  --enable-addons monitoring \
  --generate-ssh-keys
```

⏱️ This may take 5–10 minutes.

---

## ✅ Step 5: Connect to AKS Cluster

```bash
az aks get-credentials --resource-group rondustech-rg --name rondustech-aks
```

Check access:

```bash
kubectl get nodes
```

---

# Part 2️⃣: Deploy Your Real-World App on AKS

---

## ✅ Step 6: Create Kubernetes Manifests

### 🧱 `deployment.yaml`

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

### 🌐 `service.yaml`

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

## ✅ Step 7: Apply Your Manifests

```bash
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
```

---

## ✅ Step 8: Access Your App

### Get External IP:

```bash
kubectl get svc calculator-service
```

Expected output:

```
NAME                TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)        AGE
calculator-service  LoadBalancer   10.0.0.123     20.45.123.89      80:30001/TCP   2m
```

Open your browser and visit:

```
http://<EXTERNAL-IP>
```

---

## ✅ Step 9: Clean Up Resources (Optional)

```bash
kubectl delete -f deployment.yaml
kubectl delete -f service.yaml
az group delete --name rondustech-rg --yes --no-wait
```

---

## 🧠 Summary of Commands

| Task               | Command                      |
| ------------------ | ---------------------------- |
| Login to Azure     | `az login`                   |
| Create AKS Cluster | `az aks create ...`          |
| Get Credentials    | `az aks get-credentials ...` |
| Deploy App         | `kubectl apply -f ...`       |
| Get External IP    | `kubectl get svc`            |
| Delete Resources   | `kubectl delete -f ...`      |

---

