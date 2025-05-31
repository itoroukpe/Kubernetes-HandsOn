Here’s a **step-by-step practical guide** for **DevOps students** on how to write and understand **Kubernetes manifests** for the most common Kubernetes resources.

---

## 🧰 **Objective**

By the end of this guide, you’ll be able to:

* Write a Kubernetes manifest from scratch
* Understand the purpose of each resource type
* Deploy and manage an app using YAML files

---

## 🧾 Step 1: What is a Kubernetes Manifest?

A **manifest** is a YAML file that defines the **desired state** of Kubernetes resources like:

* Deployments
* Services
* ConfigMaps
* Secrets
* Ingress
* PersistentVolumeClaims
* CronJobs
* Namespaces

---

## ✍️ Step 2: Manifest File Structure

Every manifest follows this basic structure:

```yaml
apiVersion: <group/version>
kind: <ResourceType>
metadata:
  name: <resource-name>
spec:
  # Resource-specific configuration
```

---

## 🧱 Step 3: Common Kubernetes Resources

### 📦 1. Deployment – Run and manage app containers

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
        - name: myapp
          image: nginx
          ports:
            - containerPort: 80
```

---

### 🌐 2. Service – Expose internal or external access

```yaml
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
spec:
  type: NodePort
  selector:
    app: myapp
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
```

---

### 📁 3. ConfigMap – Inject non-sensitive configs

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  ENV: production
  LOG_LEVEL: info
```

---

### 🔐 4. Secret – Inject sensitive data like passwords

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: db-secret
type: Opaque
data:
  username: YWRtaW4=      # base64 for 'admin'
  password: cGFzc3dvcmQ=  # base64 for 'password'
```

---

### 💾 5. PersistentVolumeClaim – Storage for your app

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: app-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
```

---

### 🌍 6. Ingress – Route external HTTP requests

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: myapp-ingress
spec:
  rules:
    - host: myapp.local
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: myapp-service
                port:
                  number: 80
```

---

### ⏰ 7. CronJob – Run tasks on a schedule

```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: cleanup-job
spec:
  schedule: "*/5 * * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
            - name: cleanup
              image: busybox
              args:
                - /bin/sh
                - -c
                - "echo Cleaning up!"
          restartPolicy: OnFailure
```

---

## 🚀 Step 4: Deploying Manifests

### 1. Save each manifest as a `.yaml` file (e.g., `deployment.yaml`)

### 2. Apply the manifest using:

```bash
kubectl apply -f deployment.yaml
```

To apply a folder of manifests:

```bash
kubectl apply -f ./k8s/
```

---

## 🔍 Step 5: Check Resource Status

```bash
kubectl get all
kubectl describe deployment myapp-deployment
kubectl get events
```

---

## 🧹 Step 6: Clean Up

```bash
kubectl delete -f deployment.yaml
```

Or delete all at once:

```bash
kubectl delete -f ./k8s/
```

---

## 📘 Tips for Students

| Resource   | Use Case                              |
| ---------- | ------------------------------------- |
| Deployment | Run apps, manage replicas             |
| Service    | Expose apps internally or externally  |
| Ingress    | Custom routing via URLs or hostnames  |
| ConfigMap  | Environment variables & configs       |
| Secret     | Passwords and tokens (base64 encoded) |
| PVC        | Persistent storage for pods           |
| CronJob    | Scheduled tasks like backups          |

---


