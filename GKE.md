**complete DevOps student-friendly guide** to create and use **Google Kubernetes Engine (GKE)** on **Google Cloud Platform (GCP)**, including how to deploy the Docker image:

🧪 Image: `rondustech/scientific-calculator.war`

---

# ☁️ Part 1: Set Up GCP & GKE Access

## ✅ Step 1: Create a GCP Account

1. Visit: [https://cloud.google.com](https://cloud.google.com)
2. Click **“Get started for free”**
3. Sign in or create a Google account
4. Set up your billing profile (free \$300 credit available)

---

## ✅ Step 2: Create a Project

1. In the **GCP Console**, go to the **"Project Selector"**
2. Click **“New Project”**
3. Name your project (e.g., `gke-demo-project`)
4. Click **Create** and wait for it to be ready

---

## ✅ Step 3: Enable GKE and Billing APIs

From the navigation menu:

* Go to **Kubernetes Engine → Clusters**
* Click **“Enable API”** if prompted
* Also enable:

  * Compute Engine API
  * Container Registry API

---

## ✅ Step 4: Install the Google Cloud CLI (gcloud)

### 🔧 Install (Linux/macOS):

```bash
curl -O https://dl.google.com/dl/cloudsdk/channels/rapid/downloads/google-cloud-cli-442.0.0-linux-x86_64.tar.gz
tar -xf google-cloud-cli-*.tar.gz
./google-cloud-sdk/install.sh
```

Restart your terminal and run:

```bash
gcloud init
```

* Log in
* Select your project
* Set your region and zone (e.g., `us-west1-a`)

---

## ✅ Step 5: Install kubectl

If you used `gcloud init`, it will auto-install `kubectl`.

Verify:

```bash
kubectl version --client
```

---

## ✅ Step 6: Create a GKE Cluster

```bash
gcloud container clusters create scientific-cluster \
  --zone us-west1-a \
  --num-nodes 2 \
  --enable-ip-alias
```

This will provision a 2-node GKE cluster in your project.

---

## ✅ Step 7: Connect kubectl to GKE

```bash
gcloud container clusters get-credentials scientific-cluster --zone us-west1-a
```

Test with:

```bash
kubectl get nodes
```

---

# 🚀 Part 2: Deploy a Real App to GKE

Image: `rondustech/scientific-calculator.war`

---

## 📁 Step 1: Create Kubernetes Manifests

Create a folder called `gke-app/`, and inside it, add:

---

### 1. `deployment.yaml`

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

### 2. `service.yaml`

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
    - protocol: TCP
      port: 80
      targetPort: 8080
```

---

## 📥 Step 2: Deploy to GKE

```bash
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
```

---

## 🔍 Step 3: Verify the Deployment

```bash
kubectl get pods
kubectl get svc calculator-service
```

You’ll see the external IP under `EXTERNAL-IP`. It may take a minute to appear.

Visit:

```
http://<EXTERNAL-IP>
```

---

## 🧹 Step 4: Clean Up Resources

```bash
kubectl delete -f deployment.yaml
kubectl delete -f service.yaml
```

To delete the cluster:

```bash
gcloud container clusters delete scientific-cluster --zone us-west1-a
```

---

## 🧠 Summary

| Task               | Command / Action                                          |
| ------------------ | --------------------------------------------------------- |
| GCP Account        | cloud.google.com                                          |
| Create project     | GCP Console                                               |
| Enable APIs        | Kubernetes Engine API                                     |
| Create GKE Cluster | `gcloud container clusters create ...`                    |
| Connect to GKE     | `gcloud container clusters get-credentials`               |
| Deploy app         | `kubectl apply -f`                                        |
| Access app         | `kubectl get svc`                                         |
| Clean up           | `kubectl delete -f` or `gcloud container clusters delete` |

---


