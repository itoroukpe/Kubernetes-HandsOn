Excellent! Let’s go step-by-step to cover these advanced Kubernetes topics for your **Rondustech DevOps students**:

---

## ✅ 1. **Helm Installation & Usage**

### 📦 Install Helm

```bash
curl https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 | bash
helm version
```

### 🔹 Add a Chart Repository & Update

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update
```

### 🚀 Install an App via Helm (e.g., Apache)

```bash
helm install my-apache bitnami/apache
kubectl get svc
```

### 🧹 Cleanup

```bash
helm uninstall my-apache
```

---

## ✅ 2. **Add Ingress, Volumes, and Secrets**

---

### 🌐 **Ingress Controller Setup (Minikube)**

```bash
minikube addons enable ingress
```

#### ✍️ Sample Ingress Resource

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: web-ingress
spec:
  rules:
  - host: example.local
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: nginx
            port:
              number: 80
```

> Then add `example.local` to your local `/etc/hosts` pointing to `minikube ip`

---

### 💾 **Volumes (PVC) Example**

#### Create PersistentVolumeClaim

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mypvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
```

Apply:

```bash
kubectl apply -f pvc.yaml
```

#### Mount PVC in Pod

```yaml
volumes:
- name: myvolume
  persistentVolumeClaim:
    claimName: mypvc

volumeMounts:
- mountPath: "/data"
  name: myvolume
```

---

### 🔐 **Secrets Example**

#### Create a Secret

```bash
kubectl create secret generic db-secret \
  --from-literal=username=admin \
  --from-literal=password=secret123
```

#### Use Secret in Deployment

```yaml
env:
- name: DB_USER
  valueFrom:
    secretKeyRef:
      name: db-secret
      key: username
- name: DB_PASS
  valueFrom:
    secretKeyRef:
      name: db-secret
      key: password
```

---

