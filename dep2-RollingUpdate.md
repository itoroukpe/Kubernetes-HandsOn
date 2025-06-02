```
# Deployments  Rolling Update
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 2
  strategy:
    type: RollingUpdate
    rollingUpdate:
       maxSurge: 1
       maxUnavailable: 1
  minReadySeconds: 30   
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      name: nginx-pod
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx-container
        image: nginx:1.7.9
        ports:
        - containerPort: 80
```
---
Here's a **student-friendly explanation** of the Kubernetes (K8s) manifest file you've provided, which defines a **Deployment** using a **Rolling Update** strategy:

---

## 🔍 Purpose of the File

This manifest file creates a **Deployment** in Kubernetes that runs **2 replicas** (copies) of an NGINX container. It uses a **rolling update strategy** to make sure updates happen **gradually**, without downtime.

---

## 🧾 Section-by-Section Breakdown

### 🔹 `apiVersion: apps/v1`

* This tells Kubernetes which API version you're using.
* `apps/v1` is the current stable version for Deployments.

---

### 🔹 `kind: Deployment`

* You're creating a **Deployment** object, which manages replicas and updates of Pods automatically.

---

### 🔹 `metadata:`

* `name: nginx-deployment`: The name of this deployment is `nginx-deployment`.

---

### 🔹 `spec:`

Defines **how the deployment behaves**.

#### 🧮 `replicas: 2`

* You want **2 running Pods** of your NGINX container.

---

#### 🔁 `strategy: RollingUpdate`

This is how updates are done **without downtime**:

* **`maxSurge: 1`**
  During the update, you can run **1 extra Pod** temporarily (total = 3 max) to ensure availability.

* **`maxUnavailable: 1`**
  At most, **1 Pod can be unavailable** during the update (i.e., being replaced).

---

#### ⏳ `minReadySeconds: 30`

* A Pod must be **running and ready for at least 30 seconds** before it's considered healthy and the next Pod update begins.

---

### 🔹 `selector:`

Tells the Deployment **which Pods it should manage**, using labels.

```yaml
matchLabels:
  app: nginx
```

* This ensures only Pods with `app: nginx` label are controlled by this deployment.

---

### 🔹 `template:`

This defines the actual **Pod template** — the "blueprint" for each replica.

#### `metadata:`

```yaml
name: nginx-pod
labels:
  app: nginx
```

* The Pod is labeled `app: nginx`, which matches the selector above.

#### `spec:`

Defines the **containers** that will run in each Pod.

```yaml
containers:
- name: nginx-container
  image: nginx:1.7.9
  ports:
  - containerPort: 80
```

* `name`: The container is named `nginx-container`
* `image`: Uses the Docker image `nginx:1.7.9`
* `containerPort: 80`: Exposes port 80 inside the container (used for web traffic)

---

## 🎓 Summary for Students

| Component       | Meaning                                                   |
| --------------- | --------------------------------------------------------- |
| Deployment      | Manages multiple copies of a containerized app            |
| RollingUpdate   | Updates containers **one-by-one** with minimal disruption |
| replicas: 2     | Ensures **2 instances** of the app are always running     |
| maxSurge        | Allows adding **1 extra Pod temporarily** during update   |
| maxUnavailable  | Allows **1 Pod to go down** during update                 |
| minReadySeconds | Waits 30 sec before marking a Pod as “ready”              |
| selector        | Matches Pods using a **label** (`app: nginx`)             |
| container image | Runs `nginx:1.7.9`, a lightweight web server image        |

---


