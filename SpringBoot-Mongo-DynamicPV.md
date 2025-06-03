```
# Complete Manifest Where in single yml we defined Deployment & Service for SpringApp & PVC(with default  StorageClass),ReplicaSet & Service For Mongo.
# Deployment for Spring Boot Application
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
      - name: springapp-container
        image: rondustech/spring-boot-mongo  # Updated image
        ports:
        - containerPort: 8080
        env:
        - name: MONGO_DB_USERNAME
          value: devdb
        - name: MONGO_DB_PASSWORD
          value: devdb@123
        - name: MONGO_DB_HOSTNAME
          value: mongo
---
# NodePort Service to expose Spring App outside the cluster
apiVersion: v1
kind: Service
metadata:
  name: springapp-service
spec:
  type: NodePort
  selector:
    app: springapp
  ports:
  - port: 80              # Service port
    targetPort: 8080      # Container port
    nodePort: 30008       # Optional: Fixed external port (range: 30000–32767)
---
# Persistent Volume Claim for MongoDB data storage
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
---
# ReplicaSet for MongoDB with PVC mounted
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
      - name: mongo-data
        persistentVolumeClaim:
          claimName: mongodb-pvc
      containers:
      - name: mongodb-container
        image: mongo
        ports:
        - containerPort: 27017
        env:
        - name: MONGO_INITDB_ROOT_USERNAME
          value: devdb
        - name: MONGO_INITDB_ROOT_PASSWORD
          value: devdb@123
        volumeMounts:
        - name: mongo-data
          mountPath: /data/db
---
# ClusterIP Service for internal MongoDB connection
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
Here’s a clear, **step-by-step explanation** of the Kubernetes manifest file for DevOps students. This file sets up a **Spring Boot app with a MongoDB database**, using **Deployment**, **ReplicaSet**, **Services**, and a **PersistentVolumeClaim** — all in one `.yml` file.

---

## 🔍 Overview

The manifest includes:

1. **Deployment** for the Spring Boot app
2. **NodePort Service** to access Spring app externally
3. **PersistentVolumeClaim (PVC)** for MongoDB data
4. **ReplicaSet** to manage MongoDB Pod
5. **ClusterIP Service** for internal MongoDB access

---

## 🧩 Section-by-Section Breakdown

---

### 🟦 **1. Spring Boot Deployment**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: springapp-deployment
```

* **Deployment** manages app lifecycle and ensures there are always 2 Pods (`replicas: 2`) running for high availability.

```yaml
spec:
  replicas: 2
  selector:
    matchLabels:
      app: springapp
```

* Selects Pods with the label `app: springapp`.

```yaml
  template:
    metadata:
      labels:
        app: springapp
```

* This is the **Pod template**. Any Pod created by this Deployment will have the label `app: springapp`.

```yaml
    spec:
      containers:
      - name: springapp-container
        image: rondustech/spring-boot-mongo
```

* Uses the image for the Spring Boot app. It exposes `8080` and passes MongoDB credentials as environment variables.

```yaml
        env:
        - name: MONGO_DB_USERNAME
          value: devdb
```

* These env variables are passed to the app so it can connect to MongoDB.

---

### 🌐 **2. Service for Spring App**

```yaml
kind: Service
metadata:
  name: springapp-service
spec:
  type: NodePort
```

* **NodePort** allows external access to the Spring Boot app via a port on the Node (here, port `30008`).

```yaml
  ports:
  - port: 80
    targetPort: 8080
    nodePort: 30008
```

* Maps external requests on port `30008` to port `8080` inside the Pod.

---

### 💾 **3. PersistentVolumeClaim (PVC)**

```yaml
kind: PersistentVolumeClaim
metadata:
  name: mongodb-pvc
```

* Requests 1Gi of storage for MongoDB data. The storage is retained even if the Pod is deleted.

```yaml
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
```

---

### 🛡️ **4. ReplicaSet for MongoDB**

```yaml
kind: ReplicaSet
metadata:
  name: mongodb-replicaset
```

* Ensures **one MongoDB Pod** is always running.

```yaml
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mongodb
```

* Uses the label `app: mongodb` to identify its Pods.

```yaml
      volumes:
      - name: mongo-data
        persistentVolumeClaim:
          claimName: mongodb-pvc
```

* Mounts the PVC we created earlier.

```yaml
      containers:
      - name: mongodb-container
        image: mongo
        env:
        - name: MONGO_INITDB_ROOT_USERNAME
          value: devdb
```

* Sets up environment variables for MongoDB root user and mounts the volume to `/data/db`.

---

### 🔗 **5. ClusterIP Service for Mongo**

```yaml
kind: Service
metadata:
  name: mongo
spec:
  type: ClusterIP
```

* This is an **internal service** that exposes MongoDB to other Pods **within the cluster** (e.g., the Spring app).

```yaml
  selector:
    app: mongodb
  ports:
  - port: 27017
```

* Makes MongoDB available internally at `mongo:27017`.

---

## ✅ Summary

| Resource                | Purpose                                        |
| ----------------------- | ---------------------------------------------- |
| `Deployment`            | Deploys and manages Spring Boot app            |
| `NodePort Service`      | Exposes the Spring app outside the cluster     |
| `PersistentVolumeClaim` | Provides durable storage for MongoDB           |
| `ReplicaSet`            | Runs MongoDB Pod and ensures availability      |
| `ClusterIP Service`     | Allows Spring app to access MongoDB internally |

---







