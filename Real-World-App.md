## ✅ 3. Deploy a **Real-World App** via Helm

---

### 🚀 **Option A: WordPress with MySQL (via Helm)**

```bash
helm install my-blog bitnami/wordpress
```

Get the admin password:

```bash
kubectl get secret --namespace default my-blog-wordpress -o jsonpath="{.data.wordpress-password}" | base64 --decode
```

Access:

```bash
minikube service my-blog-wordpress
```

---

### 🚀 **Option B: Spring Boot App (Custom Deployment)**

#### 1. Build Docker Image (locally or via GitHub Container Registry)

```Dockerfile
FROM openjdk:17-jdk
COPY target/demo.jar app.jar
ENTRYPOINT ["java", "-jar", "/app.jar"]
```

Push to DockerHub:

```bash
docker build -t yourdockerhub/springapp .
docker push yourdockerhub/springapp
```

#### 2. Create K8s Deployment

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: springapp
spec:
  replicas: 1
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
        image: yourdockerhub/springapp
        ports:
        - containerPort: 8080
```

#### 3. Expose and Access App

```bash
kubectl expose deployment springapp --type=NodePort --port=8080
minikube service springapp
```

---



---


