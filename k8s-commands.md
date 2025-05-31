 **Kubernetes commands with Minikube**. 
 This will help students become fluent in deploying, managing, and troubleshooting Kubernetes workloads using the `kubectl` CLI on a Minikube cluster.

---

# 🧑‍💻 **Kubernetes Command Guide for Minikube**

## 🔧 Prerequisite

Start your Minikube cluster:

```bash
minikube start
```

---

## 🚀 1. **Basic Minikube Commands**

```bash
minikube status            # Check Minikube status
minikube dashboard         # Open Minikube dashboard in browser
minikube ip                # Get Minikube cluster IP
minikube stop              # Stop the cluster
minikube delete            # Delete the cluster
```

---

## 📦 2. **Deploy an App Using kubectl**

### Create a Deployment:

```bash
kubectl create deployment nginx --image=nginx
```

### View Pods:

```bash
kubectl get pods
kubectl describe pod <pod-name>
```

### Expose the Deployment:

```bash
kubectl expose deployment nginx --type=NodePort --port=80
```

### Access the Service:

```bash
minikube service nginx
```

---

## ✍️ 3. **Apply YAML Manifests**

Save the manifest as `deployment.yaml`, then apply:

```bash
kubectl apply -f deployment.yaml
```

To apply all manifests in a folder:

```bash
kubectl apply -f ./k8s/
```

---

## 🔄 4. **Update or Scale Your App**

### Scale replicas:

```bash
kubectl scale deployment nginx --replicas=3
```

### Update container image:

```bash
kubectl set image deployment/nginx nginx=nginx:1.25
```

---

## 🧪 5. **Inspecting Resources**

### List resources:

```bash
kubectl get all
kubectl get deployments
kubectl get services
kubectl get pods
```

### Describe resource in detail:

```bash
kubectl describe deployment nginx
```

### View logs from a pod:

```bash
kubectl logs <pod-name>
```

---

## 🧼 6. **Clean Up**

### Delete resources:

```bash
kubectl delete deployment nginx
kubectl delete service nginx
```

### Delete all resources in a file:

```bash
kubectl delete -f deployment.yaml
```

---

## 🌐 7. **Ingress Access (if enabled)**

Enable Ingress in Minikube:

```bash
minikube addons enable ingress
```

Get the Minikube IP and update `/etc/hosts`:

```bash
minikube ip
# e.g., 192.168.49.2
```

```bash
# Add to /etc/hosts
192.168.49.2 trackonomy.local
```

Then apply an Ingress manifest and access `http://trackonomy.local`.

---

## 🔐 8. **Working with Secrets & ConfigMaps**

### Create a ConfigMap:

```bash
kubectl create configmap app-config --from-literal=ENV=dev
```

### Create a Secret:

```bash
kubectl create secret generic db-secret --from-literal=password=Pa$$w0rd
```

---

## 🧰 9. **Use Local Images Without Docker Hub**

Use Minikube’s Docker daemon:

```bash
eval $(minikube docker-env)
docker build -t myapp:local .
```

Reference in your manifest:

```yaml
image: myapp:local
```

---

## 📘 Summary Cheat Sheet

| Task               | Command                         |
| ------------------ | ------------------------------- |
| Start Minikube     | `minikube start`                |
| Deploy app         | `kubectl create deployment ...` |
| Expose app         | `kubectl expose deployment ...` |
| View all resources | `kubectl get all`               |
| Scale app          | `kubectl scale deployment ...`  |
| Access service     | `minikube service <name>`       |
| Logs               | `kubectl logs <pod>`            |
| Use local image    | `eval $(minikube docker-env)`   |

---


