```
# Complete Manifest Where in single yml we defined Deployment & Service for SpringApp & PVC(with default  StorageClass),ReplicaSet & Service For Mongo.
apiVersion: apps/v1
kind: Deployment
metadata:
  name: springappdeployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: springapp
  template:
    metadata:
      name: springapppod
      labels:
        app: springapp
    spec:
      containers:
      - name: springappcontainer
        image: mylandmarktech/spring-boot-mongo
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
apiVersion: v1
kind: Service
metadata:
  name: springapp
spec:
  selector:
    app: springapp
  ports:
  - port: 80
    targetPort: 8080
  type: LoadBalancer
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mongodbpvc 
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
---
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: mongodbrs
spec:
  selector:
    matchLabels:
      app: mongodb
  template:
     metadata:
       name: mongodbpod
       labels:
         app: mongodb
     spec:
       volumes:
       - name: pvc
         persistentVolumeClaim:
           claimName: mongodbpvc     
       containers:
       - name: mongodbcontainer
         image: mongo
         ports:
         - containerPort: 27017
         env:
         - name: MONGO_INITDB_ROOT_USERNAME
           value: devdb
         - name: MONGO_INITDB_ROOT_PASSWORD
           value: devdb@123
         volumeMounts:
         - name: pvc
           mountPath: /data/db   
---
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
Here is a **step-by-step explanation** of the provided Kubernetes manifest file, which defines a full-stack deployment consisting of:

* A **Spring Boot application** connected to a
* **MongoDB** database, using
* **Persistent Storage (PVC)**, with
* Associated **Services** for connectivity.

---

## 💡 Overview

This manifest includes the following Kubernetes resources:

1. **Deployment** for the Spring Boot app
2. **Service** for exposing the Spring Boot app
3. **PersistentVolumeClaim** for MongoDB data
4. **ReplicaSet** to run the MongoDB instance
5. **Service** for MongoDB

---

## 🧩 Breakdown of Each Section

---

### 🟩 1. **Deployment for SpringApp**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: springappdeployment
spec:
  replicas: 2
```

* **Creates two pods** running the Spring Boot app for load balancing and high availability.

```yaml
  selector:
    matchLabels:
      app: springapp
```

* Used to match the pods controlled by this deployment.

```yaml
  template:
    metadata:
      name: springapppod
      labels:
        app: springapp
```

* This template is what Kubernetes uses to spin up pods.

```yaml
    spec:
      containers:
      - name: springappcontainer
        image: mylandmarktech/spring-boot-mongo
        ports:
        - containerPort: 8080
```

* Runs a container based on the specified image, listening on port 8080.

```yaml
        env:
        - name: MONGO_DB_USERNAME
          value: devdb
        - name: MONGO_DB_PASSWORD
          value: devdb@123
        - name: MONGO_DB_HOSTNAME
          value: mongo
```

* Sets environment variables the Spring app uses to connect to MongoDB.

---

### 🟦 2. **Service for SpringApp**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: springapp
spec:
  selector:
    app: springapp
  ports:
  - port: 80
    targetPort: 8080
  type: LoadBalancer
```

* **Exposes the Spring Boot application** to the outside world.
* Routes external traffic from port **80** to internal container port **8080**.
* The `LoadBalancer` type is used typically in cloud environments (e.g., AWS EKS).

---

### 🟨 3. **Persistent Volume Claim (PVC) for MongoDB**

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mongodbpvc 
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
```

* Requests **1Gi** of persistent storage.
* This is attached to the MongoDB container to persist data across restarts.

---

### 🟫 4. **ReplicaSet for MongoDB**

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: mongodbrs
spec:
  selector:
    matchLabels:
      app: mongodb
```

* Ensures that **one or more MongoDB pods** stay running.

```yaml
  template:
    metadata:
      name: mongodbpod
      labels:
        app: mongodb
```

* Template for creating MongoDB pods.

```yaml
    spec:
      volumes:
      - name: pvc
        persistentVolumeClaim:
          claimName: mongodbpvc
```

* Attaches the previously defined PVC to the MongoDB container.

```yaml
      containers:
      - name: mongodbcontainer
        image: mongo
        ports:
        - containerPort: 27017
```

* Runs a MongoDB container exposing port **27017** (default MongoDB port).

```yaml
        env:
        - name: MONGO_INITDB_ROOT_USERNAME
          value: devdb
        - name: MONGO_INITDB_ROOT_PASSWORD
          value: devdb@123
```

* Initializes MongoDB with a root username and password.

```yaml
        volumeMounts:
        - name: pvc
          mountPath: /data/db
```

* Mounts the persistent volume to the MongoDB data directory.

---

### 🟪 5. **Service for MongoDB**

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

* Creates a **ClusterIP** service to expose MongoDB internally within the cluster.
* The Spring app connects to MongoDB using this internal service.

---

## 🧠 Summary for Students

| Component         | Purpose                                                  |
| ----------------- | -------------------------------------------------------- |
| **Deployment**    | Manages multiple instances (replicas) of Spring Boot app |
| **Service**       | Exposes Spring Boot app to the internet                  |
| **PVC**           | Provides persistent storage for MongoDB data             |
| **ReplicaSet**    | Ensures MongoDB pod(s) are always running                |
| **Mongo Service** | Allows Spring app to access MongoDB inside the cluster   |

---



