# Project Title

Kubernetes Monitoring Locally

# Day-1 Environment Setup

## Objective

  
Prepare the local Kubernetes environment and initialize the project structure.

## Installed Tools

- Docker
- Minikube
- Helm
- Git

## Initial Project Structure

Created the following project directories:

project/

├── docs/

├── k8s/

├── docker/

└── screenshots/

# Day-2 Deploy Demo Applications

## Objective

Deploy containerized applications inside Kubernetes and expose them using Kubernetes Services.

## Namespace Creation

Created a dedicated namespace for demo applications.

### Command Used

<div>

*kubectl create namespace demo-apps*

</div>

## Nginx Deployment

Created:

- Nginx Deployment
- Nginx Service

Files:

- **k8s/nginx/deployment.yaml**
- **k8s/nginx/service.yaml**

## Apache Deployment

Created:

- Apache Deployment
- Apache Service

Files:

- **k8s/apache/deployment.yaml**
- **k8s/apache/service.yaml**

### Commands Used

### Apply Kubernetes Manifests

```
kubectl apply -f k8s/nginx/deployment.yaml

kubectl apply -f k8s/nginx/service.yaml

kubectl apply -f k8s/apache/deployment.yaml

kubectl apply -f k8s/apache/service.yaml

```
### Verify Running Pods

```

kubectl get pods -n demo-apps

```

### Verify Services

```

kubectl get svc -n demo-apps

```

### Access Services

```

minikube service nginx-service -n demo-apps

minikube service apache-service -n demo-apps

```
# Day-3 Monitoring Stack Setup (Prometheus + Grafana + Alertmanager)

## Objective
Set up a complete monitoring system inside the Kubernetes cluster using:
    • Prometheus (metrics collection)
    • Grafana (data visualization)
    • Alertmanager (alert handling)
    • Node Exporter (node-level metrics)
Tools Used
    • Kubernetes (Minikube)
    • Helm (for installation)
    • kubectl (cluster management)

## Steps and Command Used:
### Step 1 — Create Monitoring Namespace
A dedicated namespace was created to isolate monitoring components.
```
kubectl create namespace monitoring
```

### Step 2 — Add Helm Repository
Added Prometheus community Helm charts.
```
helm repo add prometheus-community https://prometheus-community.github.io/helm-chartshelm repo update
```

### Step 3 — Configure Lightweight Setup
A custom configuration file was created to ensure the monitoring stack runs smoothly on a local system with limited resources.
```
nano k8s/monitoring/prometheus-values.yaml
```
 
### Step 4 — Install Monitoring Stack
Installed Prometheus, Grafana, Alertmanager, and exporters using Helm.
```
helm install monitoring prometheus-community/kube-prometheus-stack \  -n monitoring \  -f prometheus-values.yaml
```
### Step 5 — Verify Deployment
Checked that all monitoring components are running successfully.
```
kubectl get pods -n monitoring
```
Expected Components
Prometheus server
Grafana
Alertmanager
Node Exporter
kube-state-metrics

### Step 6 — Enable Metrics Server
Metrics Server is required for kubectl top commands.
```
minikube addons enable metrics-server
```

### Step 7 — Verify Metrics Collection
Confirmed metrics are available:
```
kubectl top nodeskubectl top pods -n demo-apps
```

### Step 8 — Access Grafana Dashboard
Port-forward Grafana service:
```
kubectl port-forward svc/monitoring-grafana -n monitoring 3000:80
```
🌐 Access URL:
http://localhost:3000
🔐 Login Credentials:
Username: admin
Password: admin

### Step 9 — Explore Default Dashboards
Verified that Grafana dashboards are working:
Kubernetes Node metrics
Pod resource usage
CPU and memory graphs

# Day-4 Application Monitoring Setup (Nginx VTS)

## Objective
Enable application-level monitoring for Nginx using the VTS (Virtual Host Traffic Status) module and integrate it with Prometheus and Grafana.

## Components Used
- Nginx (with VTS module)
- Nginx VTS Exporter
- Prometheus
- Grafana

## Setup Overview

Nginx was configured with the VTS module to expose application-level metrics through a `/status` endpoint.

A sidecar container (VTS exporter) was added to convert Nginx metrics into Prometheus format.

## Metrics Collected

- Total HTTP requests
- Request rate
- HTTP status codes (2xx success, 4xx errors)
- Traffic (bytes in/out)
- Active connections

## Verification Steps

### Check VTS Endpoint


his step validates whether the Nginx VTS (Virtual Host Traffic Status) module is correctly enabled and exposing application-level metrics.

---

#### Step 1 — Port Forward Nginx Pod

```
kubectl port-forward pod/<nginx-pod-name> -n demo-apps 8080:80

```
### Step 2 — Access the VTS Endpoint
Open the following URL in your browser:
http://localhost:8080/status

### Step 3 — Port Forward Exporter Service
Shellkubectl port-forward svc/nginx-vts-exporter -n demo-apps 9913:9913Show more lines
Open:
http://localhost:9913/metrics

### Verify in Prometheus
Expose Prometheus:
Shellkubectl port-forward svc/monitoring-kube-prometheus-prometheus -n monitoring 9090:9090Show more lines
Open:
http://localhost:9090


Run Queries
PromQLnginx_server_requests{code="total"}Show more lines
PromQLrate(nginx_server_requests{code="total"}[1m])Show more lines

Generate Test Traffic
Normal Traffic
Shellwhile true; do curl $(minikube service nginx-service -n demo-apps --url); doneShow more lines
Expected:

✅ 2xx increases
✅ total requests increase


Error Traffic (404 Simulation)
Shellwhile true; do curl $(minikube service nginx-service -n demo-apps --url)/wrongpage; doneShow more lines
Expected:

✅ 4xx increases
✅ error rate spikes


Final Outcome

✅ Application-level metrics are collected
✅ HTTP status codes (2xx, 4xx) are tracked
✅ Prometheus scraping is successful
✅ Ready for Grafana dashboards


