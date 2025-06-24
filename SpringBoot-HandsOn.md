Here is a **hands-on Kubernetes lab and YAML writing activity** for your DevOps students, using Minikube (or optionally EKS). This practical guide walks them through writing and applying Kubernetes manifests step by step.

---

## 🔧 Hands-On Lab: Deploy a Spring Boot + MongoDB App on Kubernetes

### 🧪 Lab Objective:

* Write Kubernetes manifests from scratch
* Deploy a Spring Boot app that connects to MongoDB
* Understand how `Deployment`, `Service`, `PVC`, and `ReplicaSet` work together

---

## 📋 Lab Prerequisites:

* Minikube or Kubernetes cluster (EKS if applicable)
* Docker installed
* Basic understanding of containers and YAML
* Kubernetes CLI (`kubectl`) installed

---

## 🧱 Step-by-Step Activity: Write & Apply YAML Manifests

### ✅ 1. Create Project Directory

```bash
mkdir k8s-spring-mongo-lab
cd k8s-spring-mongo-lab
```

---

### ✅ 2. Step-by-Step: Write YAML Manifests

#### 2.1 `springapp-deployment.yml`

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: springapp-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: springapp
  template:
    metadata:
      labels:
        app: springapp
    spec:
      containers:
      - name: springapp
        image: rondustech/spring-boot-mongo
        ports:
        - containerPort: 8080
        env:
        - name: MONGO_DB_USERNAME
          value: devdb
        - name: MONGO_DB_PASSWORD
          value: devdb@123
        - name: MONGO_DB_HOSTNAME
          value: mongo
```

#### 2.2 `springapp-service.yml` using NodePort

```yaml
apiVersion: v1
kind: Service
metadata:
  name: springapp-service
spec:
  type: NodePort
  selector:
    app: springapp
  ports:
  - port: 80
    targetPort: 8080
    nodePort: 30008
```
---
#### 2.2 `springapp-service.yml` using LoadBalancer
```yaml
apiVersion: v1
kind: Service
metadata:
  name: springapp-service
spec:
  type: LoadBalancer
  selector:
    app: springapp
  ports:
  - port: 80
    targetPort: 8080
```
---

#### 2.3 `mongo-pvc.yml`

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mongodb-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
```

---

#### 2.4 `mongo-replicaset.yml`

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: mongodb-replicaset
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mongodb
  template:
    metadata:
      labels:
        app: mongodb
    spec:
      volumes:
      - name: mongo-storage
        persistentVolumeClaim:
          claimName: mongodb-pvc
      containers:
      - name: mongo
        image: mongo
        ports:
        - containerPort: 27017
        env:
        - name: MONGO_INITDB_ROOT_USERNAME
          value: devdb
        - name: MONGO_INITDB_ROOT_PASSWORD
          value: devdb@123
        volumeMounts:
        - name: mongo-storage
          mountPath: /data/db
```

---

#### 2.5 `mongo-service.yml`

```yaml
apiVersion: v1
kind: Service
metadata:
  name: mongo
spec:
  type: ClusterIP
  selector:
    app: mongodb
  ports:
  - port: 27017
    targetPort: 27017
```

---

### 🚀 3. Apply YAML Files in Order

```bash
kubectl apply -f mongo-pvc.yml
kubectl apply -f mongo-replicaset.yml
kubectl apply -f mongo-service.yml
kubectl apply -f springapp-deployment.yml
kubectl apply -f springapp-service.yml
```

---

### 🔍 4. Validate Your Deployment

```bash
kubectl get all
kubectl get pods
kubectl get svc
```

Access the app (on Minikube):

```bash
minikube service springapp-service
```

---


