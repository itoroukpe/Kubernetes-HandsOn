Great! Since your `springapp` is running on EKS and you already have **Prometheus and Grafana** set up, you can enable observability by configuring **Prometheus metrics exposure** in your Spring Boot app and setting up **Prometheus to scrape it**, so the data shows in **Grafana**.

---

## ✅ Overview of What Needs to Happen

1. **Enable Prometheus metrics in your Spring Boot app**
2. **Expose `/actuator/prometheus` endpoint**
3. **Ensure Kubernetes Service for your app exists**
4. **Configure Prometheus to scrape your Spring Boot app**
5. **Import a dashboard in Grafana to visualize the metrics**

---

## 🔧 Step-by-Step Instructions

---

### 1️⃣ Add Micrometer + Actuator to Your Spring Boot App

Make sure these dependencies are in your `pom.xml` (for Maven):

```xml
<dependency>
  <groupId>io.micrometer</groupId>
  <artifactId>micrometer-registry-prometheus</artifactId>
</dependency>
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

---

### 2️⃣ Configure Application Properties

In `application.properties` or `application.yml`:

```properties
management.endpoints.web.exposure.include=*
management.endpoint.prometheus.enabled=true
management.metrics.export.prometheus.enabled=true
management.server.port=8080
```

Or in YAML:

```yaml
management:
  endpoints:
    web:
      exposure:
        include: "*"
  endpoint:
    prometheus:
      enabled: true
  metrics:
    export:
      prometheus:
        enabled: true
```

---

### 3️⃣ Expose Your App via Kubernetes Service

Make sure your app is reachable via a Kubernetes `Service`.

Example `Service` definition:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: springapp
  labels:
    app: springapp
spec:
  selector:
    app: springapp
  ports:
  - protocol: TCP
    port: 8080
    targetPort: 8080
```

---

### 4️⃣ Edit Prometheus Scrape Config (via Helm)

Prometheus needs to know how to **scrape** your app's `/actuator/prometheus` endpoint.

If you installed Prometheus via Helm, you can **patch the `kube-prometheus-stack` values**:

#### Option 1: Update Helm values (preferred)

1. Get current values:

```bash
helm get values prometheus -n monitoring > current-values.yaml
```

2. Add a custom scrape config under `prometheus.prometheusSpec.additionalScrapeConfigs`:

```yaml
prometheus:
  prometheusSpec:
    additionalScrapeConfigs:
      - job_name: 'springapp'
        metrics_path: /actuator/prometheus
        kubernetes_sd_configs:
          - role: endpoints
        relabel_configs:
          - source_labels: [__meta_kubernetes_service_label_app]
            action: keep
            regex: springapp
```

3. Upgrade Prometheus:

```bash
helm upgrade prometheus prometheus-community/kube-prometheus-stack \
  -f current-values.yaml -n monitoring
```

#### Option 2: Create a `ConfigMap` with `additional-scrape-configs.yaml` (if `values.yaml` is too complex)

Let me know if you prefer this method.

---

### 5️⃣ Verify Prometheus is Scraping Your App

Go to Prometheus UI:

```bash
kubectl port-forward svc/prometheus-kube-prometheus-prometheus -n monitoring 9090
```

Then open [http://localhost:9090](http://localhost:9090) → **Status > Targets**

✅ You should see `springapp` as a target and `UP`.

---

### 6️⃣ View Metrics in Grafana

1. Open Grafana (`http://localhost:3000`)
2. Log in and go to:

   * **Dashboards > New > Import**
3. Use one of these Spring Boot dashboard IDs from [grafana.com](https://grafana.com/grafana/dashboards):

   * 📊 **Dashboard ID**: `4701` (Spring Boot metrics dashboard)
4. Select your **Prometheus data source**, and click **Import**

---

### ✅ Done!

Now your `springapp` metrics are visible in Grafana and scraped by Prometheus 🎉

---


