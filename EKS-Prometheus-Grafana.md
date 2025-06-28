Here is a **step-by-step guide** to implement **Prometheus and Grafana** on an **EKS (Amazon Elastic Kubernetes Service)** cluster:

---

## 🚀 Step-by-Step Guide to Deploy Prometheus & Grafana on EKS

### 🔧 Prerequisites

* An existing **EKS cluster**
* **kubectl** configured for your EKS cluster
* **Helm v3+** installed
* IAM permissions to create resources (ServiceAccounts, IAM roles, etc.)

---
## First: Let's install Helm v3+

## ✅ What is Helm?

**Helm** is the package manager for Kubernetes. It allows you to define, install, and upgrade even the most complex Kubernetes applications using “charts.”

---

## 🖥️ Step-by-Step Installation of Helm v3+

### ✅ Step 1: Check Existing Version (if any)

```bash
helm version
```

If it returns a version < `v3.0.0`, or “command not found,” continue below.

---

### 🟦 Option 1: Install via Script (Linux / Mac)

#### 🔹 For Linux or macOS:

```bash
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
```

> ⚠️ This will download and install the latest Helm v3 release.

---

### 🟦 Option 2: Install via Package Manager

#### macOS with Homebrew:

```bash
brew install helm
```

#### Ubuntu/Debian:

```bash
sudo apt-get update
sudo apt-get install apt-transport-https --yes
curl https://baltocdn.com/helm/signing.asc | sudo apt-key add -
echo "deb https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list
sudo apt-get update
sudo apt-get install helm
```

#### RHEL/CentOS/Fedora:

```bash
curl https://baltocdn.com/helm/signing.asc | sudo rpm --import -
sudo yum install yum-utils
sudo yum-config-manager --add-repo https://baltocdn.com/helm/stable/rpm/
sudo yum install helm
```

---

### 🟦 Option 3: Install from Binary Release (Any OS)

1. Go to [https://github.com/helm/helm/releases](https://github.com/helm/helm/releases)
2. Download the appropriate `helm-v3.x.x-<os>-amd64.tar.gz`
3. Unpack and move the binary to `/usr/local/bin`:

```bash
tar -zxvf helm-v3.x.x-linux-amd64.tar.gz
sudo mv linux-amd64/helm /usr/local/bin/helm
```

---

### ✅ Step 2: Verify Installation

```bash
helm version
```

You should see something like:

```
version.BuildInfo{Version:"v3.14.0", ...}
```


---
## 1️⃣ Set Up Your Namespace

```bash
kubectl create namespace monitoring
```

---

## 2️⃣ Add the Prometheus Community Helm Repo

```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
```

---

## 3️⃣ Install Prometheus via Helm

```bash
helm install prometheus prometheus-community/kube-prometheus-stack \
  --namespace monitoring
```

This will install:

* Prometheus server
* Alertmanager
* Grafana
* node-exporter
* kube-state-metrics

✅ **Check that everything is running:**

```bash
kubectl get pods -n monitoring
```

---

## 4️⃣ Access Grafana UI


> 🔴 **You cannot access Grafana on `http://<EKS-IP>:3000` just by running `kubectl port-forward`.**

Here's why:

---

## ❗ Why You Can’t Use the EKS Public IP

* `kubectl port-forward` binds **only to your local machine (127.0.0.1)**.
* It does **not expose** the service externally or to the node IP.
* So, `http://34.216.116.60:3000` won’t work unless you **explicitly expose Grafana** via a **LoadBalancer** or **Ingress**.

---

## ✅ How to Expose Grafana on EKS

### 🔄 Option 1: Change Service Type to `LoadBalancer`

This is the easiest way to expose Grafana publicly.

```bash
kubectl patch svc prometheus-grafana -n monitoring -p '{"spec": {"type": "LoadBalancer"}}'
```

Then get the external IP:

```bash
kubectl get svc prometheus-grafana -n monitoring
```

Look under the `EXTERNAL-IP` column. Once it appears, visit:

```
http://<EXTERNAL-IP>:80
```

(Note: It may take a few minutes for AWS to assign the IP.)


---
### Default credentials:

* **Username**: `admin`
* **Password**: `prom-operator` (or get it with the command below)

```bash
kubectl get secret -n monitoring prometheus-grafana -o jsonpath="{.data.admin-password}" | base64 --decode
```

---

## 5️⃣ View Prometheus UI (Optional)

```bash
kubectl port-forward svc/prometheus-kube-prometheus-prometheus -n monitoring 9090:9090
```

Then go to: [http://localhost:9090](http://localhost:9090)

---

## 6️⃣ Set Up Dashboards in Grafana

Grafana comes with built-in dashboards from the Helm chart. You can also:

* Add **Kubernetes/Node/Pod dashboards** via JSON imports from [Grafana Dashboards](https://grafana.com/grafana/dashboards/)
* Use **Prometheus** as the default data source (already configured)

---

## 7️⃣ (Optional) Expose Grafana with LoadBalancer

Edit values before install, or patch the service after install:

```bash
kubectl patch svc prometheus-grafana -n monitoring -p '{"spec": {"type": "LoadBalancer"}}'
```

Then run:

```bash
kubectl get svc prometheus-grafana -n monitoring
```

Note the **EXTERNAL-IP**, then access Grafana via `http://<external-ip>`

---

## 8️⃣ Configure IAM Roles for Service Accounts (IRSA) \[If Needed]

For more secure AWS integration (e.g., CloudWatch metrics), configure IRSA:

```bash
eksctl create iamserviceaccount \
  --name prometheus \
  --namespace monitoring \
  --cluster <your-cluster-name> \
  --attach-policy-arn arn:aws:iam::aws:policy/CloudWatchReadOnlyAccess \
  --approve
```

Then patch the Prometheus deployment to use this service account.

---

## 9️⃣ (Optional) Enable Persistent Storage

To persist metrics even after pod restarts, modify the Helm values:

```bash
helm show values prometheus-community/kube-prometheus-stack > values.yaml
```

Then edit `values.yaml`:

```yaml
prometheus:
  prometheusSpec:
    storageSpec:
      volumeClaimTemplate:
        spec:
          accessModes: ["ReadWriteOnce"]
          resources:
            requests:
              storage: 20Gi
```

Then reinstall with:

```bash
helm upgrade --install prometheus prometheus-community/kube-prometheus-stack \
  -f values.yaml \
  --namespace monitoring
```

---

## ✅ Summary

| Component  | Access Method                               |
| ---------- | ------------------------------------------- |
| Grafana    | `http://localhost:3000`                     |
| Prometheus | `http://localhost:9090`                     |
| Metrics    | Kubernetes metrics + node-exporter          |
| Dashboards | Auto-loaded into Grafana or import manually |

---


