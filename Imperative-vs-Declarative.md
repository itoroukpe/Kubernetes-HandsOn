A comprehensive list of imperative and declarative Kubernetes commands 
---

### **Imperative Commands**
Imperative commands are used directly in the command line to manage Kubernetes resources.

#### **1. Pods**
- **Create a Pod**
  ```bash
  kubectl run my-pod --image=nginx --restart=Never
  ```

- **Delete a Pod**
  ```bash
  kubectl delete pod my-pod
  ```

#### **2. Deployments**
- **Create a Deployment**
  ```bash
  kubectl create deployment my-deployment --image=nginx
  ```

- **Scale a Deployment**
  ```bash
  kubectl scale deployment my-deployment --replicas=3
  ```

- **Update the Deployment**
  ```bash
  kubectl set image deployment/my-deployment nginx=nginx:1.21 --record
  ```
```bash
kubectl get deployment -o wide
```
#### **3. Services**
- **Create a Service**
  ```bash
  kubectl expose deployment my-deployment --type=NodePort --port=80
  ```

#### **4. DaemonSets**
- **Create a DaemonSet**
  ```bash
  kubectl create daemonset my-daemonset --image=nginx
  ```

#### **5. ConfigMaps**
- **Create a ConfigMap**
  ```bash
  kubectl create configmap my-config --from-literal=key1=value1 --from-literal=key2=value2
  ```
#### **6. Creating a yaml file
```bash
kubectl create deployment my-deployment --image=nginx --dry-run=client -o yaml
```
```bash
kubectl create deployment my-deployment --image=nginx --dry-run=client -o yaml > my-nginx.yaml
```
---

### **Declarative Commands**
Declarative commands rely on YAML manifests to describe resources and their desired state.

#### **1. Pods**
- **Example YAML**
  ```yaml
  apiVersion: v1
  kind: Pod
  metadata:
    name: my-pod
  spec:
    containers:
    - name: nginx
      image: nginx
  ```
- **Apply the YAML**
  ```bash
  kubectl apply -f pod.yaml
  ```

#### **2. DaemonSets**
- **Example YAML**
  ```yaml
  apiVersion: apps/v1
  kind: DaemonSet
  metadata:
    name: my-daemonset
  spec:
    selector:
      matchLabels:
        app: nginx
    template:
      metadata:
        labels:
          app: nginx
      spec:
        containers:
        - name: nginx
          image: nginx:1.21
  ```
- **Apply the YAML**
  ```bash
  kubectl apply -f daemonset.yaml
  ```

#### **3. Taints and Tolerations**
- **Add a Taint**
  ```bash
  kubectl taint nodes my-node key=value:NoSchedule
  ```

- **Toleration Example YAML**
  ```yaml
  apiVersion: v1
  kind: Pod
  metadata:
    name: pod-with-toleration
  spec:
    tolerations:
    - key: "key"
      operator: "Equal"
      value: "value"
      effect: "NoSchedule"
    containers:
    - name: nginx
      image: nginx
  ```
- **Apply the YAML**
  ```bash
  kubectl apply -f toleration-pod.yaml
  ```

#### **4. Networking (Services, Ingress)**
- **Service YAML**
  ```yaml
  apiVersion: v1
  kind: Service
  metadata:
    name: my-service
  spec:
    selector:
      app: my-app
    ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
    type: NodePort
  ```
- **Ingress YAML**
  ```yaml
  apiVersion: networking.k8s.io/v1
  kind: Ingress
  metadata:
    name: my-ingress
  spec:
    rules:
    - host: my-app.local
      http:
        paths:
        - path: /
          pathType: Prefix
          backend:
            service:
              name: my-service
              port:
                number: 80
  ```

- **Apply the YAML**
  ```bash
  kubectl apply -f service.yaml
  kubectl apply -f ingress.yaml
  ```

#### **5. Resource Limits**
- **Pod with Resource Limits**
  ```yaml
  apiVersion: v1
  kind: Pod
  metadata:
    name: resource-limits-pod
  spec:
    containers:
    - name: nginx
      image: nginx
      resources:
        requests:
          memory: "64Mi"
          cpu: "250m"
        limits:
          memory: "128Mi"
          cpu: "500m"
  ```
- **Apply the YAML**
  ```bash
  kubectl apply -f resource-limits.yaml
  ```

#### **6. Labels and Selectors**
- **Apply a Label to a Node**
  ```bash
  kubectl label nodes my-node disktype=ssd
  ```

- **Pod with Label Selector**
  ```yaml
  apiVersion: v1
  kind: Pod
  metadata:
    name: labeled-pod
    labels:
      app: my-app
  spec:
    containers:
    - name: nginx
      image: nginx
  ```

#### **7. Node Affinity**
- **Node Affinity YAML**
  ```yaml
  apiVersion: v1
  kind: Pod
  metadata:
    name: affinity-pod
  spec:
    affinity:
      nodeAffinity:
        requiredDuringSchedulingIgnoredDuringExecution:
          nodeSelectorTerms:
          - matchExpressions:
            - key: disktype
              operator: In
              values:
              - ssd
    containers:
    - name: nginx
      image: nginx
  ```
- **Apply the YAML**
  ```bash
  kubectl apply -f node-affinity.yaml
  ```

---

### **Summary Table of Imperative vs Declarative**
| **Resource**        | **Imperative Command Example**                  | **Declarative YAML Example**                        |
|----------------------|------------------------------------------------|----------------------------------------------------|
| Pod                 | `kubectl run my-pod --image=nginx`             | YAML with `kind: Pod`                             |
| Deployment          | `kubectl create deployment my-deployment`      | YAML with `kind: Deployment`                      |
| DaemonSet           | `kubectl create daemonset my-daemonset`        | YAML with `kind: DaemonSet`                       |
| Service             | `kubectl expose pod my-pod --type=NodePort`    | YAML with `kind: Service`                         |
| Ingress             | N/A                                            | YAML with `kind: Ingress`                         |
| Taints & Tolerations| `kubectl taint nodes my-node key=value:NoExec` | YAML under `spec.tolerations`                     |
| Node Affinity       | N/A                                            | YAML under `spec.affinity.nodeAffinity`           |
| Resource Limits     | N/A                                            | YAML under `spec.containers[].resources`          |

Use **imperative commands** for quick actions or experimentation, and **declarative YAML** for repeatable and version-controlled configurations!
