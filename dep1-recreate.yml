```
# Deployments  Recreate
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 2
  strategy:
    type: Recreate
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
Here’s a **line-by-line breakdown** of the Kubernetes manifest you provided, explained in a way that's easy for **DevOps students** to understand. This file defines a **Deployment** that manages an NGINX web server.

---

## 🔧 What is This File?

This is a **Kubernetes manifest** written in YAML. It tells Kubernetes how to create and manage a **Deployment** of NGINX containers.

---

### 📄 Manifest Breakdown

```yaml
# Deployments  Recreate
```

💬 *This is just a comment — helpful for humans to understand the type of deployment and strategy being used ("Recreate").*

---

```yaml
apiVersion: apps/v1
```

📌 **apiVersion** – Defines which Kubernetes API version this file uses.
Here, `apps/v1` is used for Deployments.

---

```yaml
kind: Deployment
```

📦 **kind** – Declares the type of Kubernetes object.
This one is a **Deployment**, which manages replica sets and pod lifecycles.

---

```yaml
metadata:
  name: nginx-deployment
```

📝 **metadata** – Metadata provides identifying information.
`name` gives the deployment a name (`nginx-deployment`), which you'll use when managing it (e.g., `kubectl get deployment nginx-deployment`).

---

```yaml
spec:
  replicas: 2
```

⚙️ **spec** – The specification or desired state.
Here, it says to run **2 pods** of the application.

---

```yaml
  strategy:
    type: Recreate
```

🔁 **strategy** – Defines how updates to the deployment should be handled.

* `Recreate`: Deletes **all existing pods first**, then creates new ones.
* Use this when you don't want multiple versions running at the same time (e.g., stateful apps).

Most apps use `RollingUpdate` (the default), but `Recreate` is simpler for beginners.

---

```yaml
  selector:
    matchLabels:
      app: nginx
```

🔍 **selector** – Tells Kubernetes **which pods belong to this deployment** by matching their labels.

* It looks for pods with the label `app: nginx`

---

```yaml
  template:
    metadata:
      name: nginx-pod
      labels:
        app: nginx
```

🧱 **template** – This is the pod "blueprint."
It tells Kubernetes how each replica should look.

* Metadata includes labels so the deployment can manage the pods.

---

```yaml
    spec:
      containers:
      - name: nginx-container
        image: nginx:1.7.9
        ports:
        - containerPort: 80
```

📦 **containers** – A pod can contain one or more containers.

* `name`: Name of the container inside the pod.
* `image`: Docker image to use (`nginx:1.7.9` is an older version of NGINX).
* `containerPort`: Exposes port 80 inside the container (where NGINX runs by default).

---

## 🧠 Summary for Students

| Concept               | Purpose                                                                   |
| --------------------- | ------------------------------------------------------------------------- |
| **Deployment**        | Ensures a set of pods are always running (self-healing, scalable).        |
| **Recreate Strategy** | Stops all old pods before starting new ones. Useful for simpler rollouts. |
| **Selector & Labels** | Links the deployment to the right pods.                                   |
| **Template**          | Defines the pod spec (what gets deployed).                                |
| **Replicas**          | Number of pod copies Kubernetes should run.                               |

---

### ✅ Command to Deploy

```bash
kubectl apply -f nginx-deployment.yaml
```

Then check the deployment:

```bash
kubectl get deployments
kubectl get pods
```


