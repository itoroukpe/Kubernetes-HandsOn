### Kubernetes Hands-On Workshop: Deploying Jenkins, Maven, SonarQube, Nexus, and Docker

**Objective:** By the end of this workshop, participants will be able to deploy and manage a Continuous Integration/Continuous Deployment (CI/CD) stack consisting of Jenkins, Maven, SonarQube, Nexus, and Docker on a Kubernetes cluster.

---

### **Prerequisites:**

1. A working Kubernetes cluster (local or cloud-based).
2. kubectl configured and connected to the Kubernetes cluster.
3. Basic knowledge of Kubernetes concepts (Pods, Deployments, Services).
4. Helm installed on your system.
5. Access to a Docker registry (Docker Hub, Nexus, or another registry).
6. Persistent Volumes (PV) provisioner or storage class configured in the cluster.

---

### **Workshop Steps:**

#### **Step 1: Set Up the Namespace**
1. Create a namespace for the CI/CD tools:
   ```bash
   kubectl create namespace cicd
   ```
   Verify the namespace:
   ```bash
   kubectl get namespaces
   ```

---

#### **Step 2: Deploy Jenkins**

1. Create a Deployment YAML for Jenkins:
   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: jenkins
     namespace: cicd
   spec:
     replicas: 1
     selector:
       matchLabels:
         app: jenkins
     template:
       metadata:
         labels:
           app: jenkins
       spec:
         containers:
         - name: jenkins
           image: jenkins/jenkins:lts
           ports:
           - containerPort: 8080
           - containerPort: 50000
           volumeMounts:
           - name: jenkins-data
             mountPath: /var/jenkins_home
         volumes:
         - name: jenkins-data
           persistentVolumeClaim:
             claimName: jenkins-pvc
   ```

2. Apply the deployment:
   ```bash
   kubectl apply -f jenkins-deployment.yaml
   ```

3. Create a PersistentVolumeClaim (PVC) for Jenkins data:
   ```yaml
   apiVersion: v1
   kind: PersistentVolumeClaim
   metadata:
     name: jenkins-pvc
     namespace: cicd
   spec:
     accessModes:
       - ReadWriteOnce
     resources:
       requests:
         storage: 10Gi
   ```

   Apply the PVC:
   ```bash
   kubectl apply -f jenkins-pvc.yaml
   ```

4. Expose Jenkins as a service:
   ```bash
   kubectl expose deployment jenkins --type=NodePort --port=8080 --namespace=cicd
   ```
---
```bash
apiVersion: v1
kind: Service
metadata:
  name: jenkins
  namespace: cicd
spec:
  selector:
    app: jenkins
  ports:
  - port: 8080         # The port the service will expose
    targetPort: 8080   # The container port to forward traffic to
    protocol: TCP
    nodePort: 32000    # Specify a NodePort in the range 30000-32767
  type: NodePort       # Service type to expose externally
```
---

#### **Step 3: Deploy Nexus**

1. Create a Deployment YAML for Nexus:
   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: nexus
     namespace: cicd
   spec:
     replicas: 1
     selector:
       matchLabels:
         app: nexus
     template:
       metadata:
         labels:
           app: nexus
       spec:
         containers:
         - name: nexus
           image: sonatype/nexus3
           ports:
           - containerPort: 8081
           volumeMounts:
           - name: nexus-data
             mountPath: /nexus-data
         volumes:
         - name: nexus-data
           persistentVolumeClaim:
             claimName: nexus-pvc
   ```

2. Apply the deployment:
   ```bash
   kubectl apply -f nexus-deployment.yaml
   ```

3. Create a PVC for Nexus data:
   ```yaml
   apiVersion: v1
   kind: PersistentVolumeClaim
   metadata:
     name: nexus-pvc
     namespace: cicd
   spec:
     accessModes:
       - ReadWriteOnce
     resources:
       requests:
         storage: 20Gi
   ```

   Apply the PVC:
   ```bash
   kubectl apply -f nexus-pvc.yaml
   ```

4. Expose Nexus as a service:
   ```bash
   kubectl expose deployment nexus --type=NodePort --port=8081 --namespace=cicd
   ```

---

#### **Step 4: Deploy SonarQube**

1. Create a Deployment YAML for SonarQube:
   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: sonarqube
     namespace: cicd
   spec:
     replicas: 1
     selector:
       matchLabels:
         app: sonarqube
     template:
       metadata:
         labels:
           app: sonarqube
       spec:
         containers:
         - name: sonarqube
           image: sonarqube
           ports:
           - containerPort: 9000
           volumeMounts:
           - name: sonarqube-data
             mountPath: /opt/sonarqube/data
         volumes:
         - name: sonarqube-data
           persistentVolumeClaim:
             claimName: sonarqube-pvc
   ```

2. Apply the deployment:
   ```bash
   kubectl apply -f sonarqube-deployment.yaml
   ```

3. Create a PVC for SonarQube data:
   ```yaml
   apiVersion: v1
   kind: PersistentVolumeClaim
   metadata:
     name: sonarqube-pvc
     namespace: cicd
   spec:
     accessModes:
       - ReadWriteOnce
     resources:
       requests:
         storage: 10Gi
   ```

   Apply the PVC:
   ```bash
   kubectl apply -f sonarqube-pvc.yaml
   ```

4. Expose SonarQube as a service:
   ```bash
   kubectl expose deployment sonarqube --type=NodePort --port=9000 --namespace=cicd
   ```

---

#### **Step 5: Deploy Maven**

1. Maven is typically run as a build tool in Jenkins pipelines. No separate deployment is needed.
2. Install Maven inside the Jenkins image:
   - Configure Jenkins agents with Maven installed, or use a Docker container with Maven pre-installed for builds.

---

#### **Step 6: Deploy Docker Inside Kubernetes**

1. Use Kubernetes DaemonSets to ensure Docker is available on all nodes for builds:
   ```yaml
   apiVersion: apps/v1
   kind: DaemonSet
   metadata:
     name: docker
     namespace: cicd
   spec:
     selector:
       matchLabels:
         app: docker
     template:
       metadata:
         labels:
           app: docker
       spec:
         containers:
         - name: docker
           image: docker:latest
           securityContext:
             privileged: true
           volumeMounts:
           - name: dockersock
             mountPath: /var/run/docker.sock
         volumes:
         - name: dockersock
           hostPath:
             path: /var/run/docker.sock
   ```

2. Apply the DaemonSet:
   ```bash
   kubectl apply -f docker-daemonset.yaml
   ```

---

#### **Step 7: Verify the Deployment**

1. Check the status of all deployments:
   ```bash
   kubectl get pods -n cicd
   ```
2. Access services using NodePort or configure Ingress for external access.

---

#### **Step 8: Create a CI/CD Pipeline in Jenkins**

1. Configure Jenkins with:
   - SonarQube plugin for code quality checks.
   - Nexus repository for artifact storage.
   - Docker and Maven tools for builds.

2. Create a Jenkins pipeline to demonstrate:
   - Code pull from GitHub.
   - Build using Maven.
   - Code quality analysis with SonarQube.
   - Artifact storage in Nexus.
   - Docker image creation and push to a registry.

---

### **Workshop Outcome**

Participants will:
1. Deploy Jenkins, Nexus, SonarQube, and Docker on Kubernetes.
2. Set up a basic CI/CD pipeline integrating these tools.
3. Understand Kubernetes concepts like Deployments, PVCs, Services, and DaemonSets.
4. Gain hands-on experience managing a CI/CD stack in a Kubernetes environment. 

--- 

### **Next Steps:**
1. Discuss scaling strategies for the CI/CD tools.
2. Introduce monitoring tools like Prometheus and Grafana for system health.
3. Extend the pipeline with advanced CI/CD stages and deployment to Kubernetes.
