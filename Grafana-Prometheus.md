Great! Here's a **hands-on guide to setting up Prometheus and Grafana in Kubernetes using raw YAML manifests**. This is useful for learning and customizing your observability stack without Helm.

---

## 🧱 STEP-BY-STEP: Prometheus + Grafana with Raw YAML

---

### 🔹 Step 1: Create `monitoring` Namespace

```bash
kubectl create namespace monitoring
```

---

### 🔹 Step 2: Deploy Prometheus

#### 📄 prometheus-config.yaml

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: prometheus-config
  namespace: monitoring
  labels:
    name: prometheus-config
data:
  prometheus.yml: |
    global:
      scrape_interval: 15s
    scrape_configs:
      - job_name: 'prometheus'
        static_configs:
          - targets: ['localhost:9090']
```

#### 📄 prometheus-deployment.yaml

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: prometheus-deployment
  namespace: monitoring
spec:
  replicas: 1
  selector:
    matchLabels:
      app: prometheus
  template:
    metadata:
      labels:
        app: prometheus
    spec:
      containers:
      - name: prometheus
        image: prom/prometheus
        args:
          - "--config.file=/etc/prometheus/prometheus.yml"
        ports:
        - containerPort: 9090
        volumeMounts:
        - name: config-volume
          mountPath: /etc/prometheus/
      volumes:
      - name: config-volume
        configMap:
          name: prometheus-config
```

#### 📄 prometheus-service.yaml

```yaml
apiVersion: v1
kind: Service
metadata:
  name: prometheus-service
  namespace: monitoring
spec:
  type: LoadBalancer
  selector:
    app: prometheus
  ports:
  - protocol: TCP
    port: 9090
    targetPort: 9090
```

---

### 🔹 Step 3: Deploy Grafana

#### 📄 grafana-deployment.yaml

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: grafana-deployment
  namespace: monitoring
spec:
  replicas: 1
  selector:
    matchLabels:
      app: grafana
  template:
    metadata:
      labels:
        app: grafana
    spec:
      containers:
      - name: grafana
        image: grafana/grafana
        ports:
        - containerPort: 3000
```

#### 📄 grafana-service.yaml

```yaml
apiVersion: v1
kind: Service
metadata:
  name: grafana-service
  namespace: monitoring
spec:
  type: LoadBalancer
  selector:
    app: grafana
  ports:
  - protocol: TCP
    port: 3000
    targetPort: 3000
```

---

### ✅ Step 4: Apply the YAMLs

Run the following commands:

```bash
kubectl apply -f prometheus-config.yaml
kubectl apply -f prometheus-deployment.yaml
kubectl apply -f prometheus-service.yaml
kubectl apply -f grafana-deployment.yaml
kubectl apply -f grafana-service.yaml
```

---

### 🔍 Step 5: Access the Services

* **Grafana**: `http://<external-ip>:3000`
* **Prometheus**: `http://<external-ip>:9090`

Use:

```bash
kubectl get svc -n monitoring
```

To get the **EXTERNAL-IP**.

---

### 🔧 Step 6: Configure Grafana

1. Login with default:
   **User:** `admin`
   **Password:** `admin`
2. Go to **Settings → Data Sources → Add Prometheus**

   * URL: `http://prometheus-service.monitoring.svc.cluster.local:9090`
3. Import dashboard IDs or create custom ones

---

### 🧹 Cleanup (Optional)

```bash
kubectl delete namespace monitoring
```

---



---
Here is a **practical, hands-on guide** to set up **Prometheus and Grafana** in a Kubernetes cluster using **Helm**, the most efficient and production-friendly method.

---

## ✅ **Prerequisites**

1. A running Kubernetes cluster (minikube, EKS, GKE, etc.)
2. `kubectl` configured to access your cluster
3. [Helm](https://helm.sh/) installed

---

## 🛠 Step-by-Step Setup Guide

---

### 🔹 Step 1: Add Helm Repos

```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
```

---

### 🔹 Step 2: Create a Namespace (Optional but Recommended)

```bash
kubectl create namespace monitoring
```

---

### 🔹 Step 3: Install Prometheus with Helm

```bash
helm install prometheus prometheus-community/prometheus \
  --namespace monitoring
```

✅ This installs:

* Prometheus server
* Alertmanager
* Node exporter
* Pushgateway

---

### 🔹 Step 4: Verify Prometheus Deployment

```bash
kubectl get pods -n monitoring
```

Check if all pods are in `Running` state.

---

### 🔹 Step 5: Install Grafana with Helm

```bash
helm install grafana grafana/grafana \
  --namespace monitoring \
  --set adminPassword='admin123' \
  --set service.type=LoadBalancer
```

✅ `adminPassword` is the initial login password.

---

### 🔹 Step 6: Get Grafana Access Info

1. **Get the LoadBalancer IP**:

```bash
kubectl get svc grafana -n monitoring
```

If using Minikube:

```bash
minikube service grafana -n monitoring
```

2. **Login to Grafana**:

* Username: `admin`
* Password: `admin123` (or what you set above)

---

### 🔹 Step 7: Configure Grafana to Use Prometheus as a Data Source

1. Go to **http\://<Grafana-IP>:3000**
2. Click **Gear icon → Data Sources**
3. Click **Add data source**
4. Choose **Prometheus**
5. Set URL to: `http://prometheus-server.monitoring.svc.cluster.local`
6. Click **Save & Test**

---

### 🔹 Step 8: Import Dashboards

1. In Grafana, click the **+** icon → **Import**
2. Use dashboard ID like `1860` (Kubernetes Cluster Monitoring)
3. Choose your Prometheus data source
4. Click **Import**

---

## 🧪 Bonus: Port Forward (if not using LoadBalancer)

To access locally:

```bash
kubectl port-forward svc/grafana 3000:80 -n monitoring
kubectl port-forward svc/prometheus-server 9090:80 -n monitoring
```

Then visit:

* Grafana: [http://localhost:3000](http://localhost:3000)
* Prometheus: [http://localhost:9090](http://localhost:9090)

---

## 📦 Cleanup (Optional)

```bash
helm uninstall grafana -n monitoring
helm uninstall prometheus -n monitoring
kubectl delete namespace monitoring
```

---

Would you like a version using raw YAML manifests instead of Helm?
