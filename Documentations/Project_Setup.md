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



# Day‑5 Alerting Setup (Prometheus + Alertmanager)

## Objective

Configure alerting for the Nginx application using Prometheus metrics and visualize alerts in Grafana.

The goal is to detect abnormal application behavior such as:
- High traffic spikes
- High error rate (4xx)
- No incoming traffic
- High active connections

This completes the monitoring pipeline:

Metrics → Prometheus → Alerts → Grafana

---

## Components Used

- Prometheus (rule evaluation)
- Alertmanager (alert handling)
- Grafana (alert visualization)
- Nginx VTS Exporter (application metrics source)

---

## Alert Rules Configuration

A PrometheusRule resource was created to define alert conditions based on Nginx metrics.

### Applying Alert Rule

```
kubectl apply -f k8s/monitoring/nginx-alerts.yaml

```
## Alert Visualization in Grafana
Alert data was integrated into Grafana using the Prometheus ALERTS metric.
### Query used in panel:
```
max by (alertname, severity, alertstate) (  ALERTS{alertstate=~"firing|pending", alertname=~"HighNginx.*"})
```
### Visualization Type:

Table

### Output:
Displays active alerts such as:

HighNginx4xxErrors
High request rate alerts (if configured)

### Result

Application-level alerts successfully configured
Real-time detection of abnormal behavior
Alerts visible directly on Grafana dashboard
Monitoring pipeline enhanced with alerting capability


### Conclusion
Alerting adds an automated layer of observability by detecting issues without manual monitoring. By combining Nginx VTS metrics with Prometheus rules and Grafana visualization, the system can proactively identify and respond to potential problems in the application.

# Day-6 Apache and Mtail Setup

## Objective

The goal of Day‑6 is to extend monitoring capabilities by:

- Setting up Apache application
- Implementing log-based monitoring using mtail
- Parsing Apache access logs into Prometheus metrics
- Enabling application-level observability similar to Nginx VTS

This introduces a new observability approach:
- Nginx → direct metrics (VTS)
- Apache → metrics derived from logs (mtail)

## Components Used

Apached log-based Monitoring:
- Apache Server
- Mtail to parse logs into metrics
- Prometheus to Query and Store metrics
- Grafana to visalize metrics

## Steps

### Step:1 Deploying Apache

- Created deployment.yaml with Shared volumemounts for apache and Mtail
- Created apache Service

### Step:2 Mtail Integration

- Integrated Mtail using configmaps and services
- It reads apache logs and parses it to metrics
- extracted metrics like:
  - HTTP status codes
  - Requests Paths
  - Combined Path + Status code

### Step:3 Visualizing Metrics in Grafana

- Created normal Dashboard to just verify that the metrics are visible in grafana

## 8. Key Learnings

- Apache does not expose metrics natively
- Logs can be used as a reliable data source
- mtail converts logs into meaningful metrics
- Prometheus acts as the central data store
- Grafana visualizes insights from Prometheus

## 9. Conclusion

Day‑6 successfully implemented log-based monitoring using mtail, enabling:

- Application-level visibility for Apache
- Tracking of request patterns and errors
- Endpoint-level observability

This complements the Nginx VTS setup, resulting in a hybrid monitoring system using both metric-based and log-based approaches.

# Day-7 Apache Dashboard and Alerting Setup

## Objective 
- Building a Dashboard which visualizes application-level metrics.
- Traffic Spikes, Error Rates can be Visualized in this Dashboard.

## Components Used
- Grafana : To visualize Metrics
- Prometheus : To store Metrics
- Alertmanager: to Define Alert rules and trigger alert
- Mtail: To parse logs into Prometheus compatible Metrics

## Dashboard Layout
[  Request rate  ] [ Status Codes ]

[  Error rate  ] [ Error Percentage]

[Request brust detection] [Top URLs]

[No traffic Alerts] [Other Alerts]

## Alert Rules Configuration

A Prometheus Rule resource was created to define alert conditions based on Apache metrics.

### Applying Alert Rule

```
kubectl apply -f k8s/monitoring/apache-alerts.yaml

```
## Alert Visualization in Grafana
Alert data was integrated into Grafana using the Prometheus ALERTS metric.

### Query used in panel:
```
max by (alertname, severity, alertstate) (  ALERTS{alertstate=~"firing|pending", alertname=~"HighApache.*"})
```

### Visualization Type:

Table

### Output:
Displays active alerts such as:

HighApacheErrors
HighApacheTraffic

### Result

Application-level alerts successfully configured
Real-time detection of abnormal behavior
Visualized Metrics in Grafana Dashboard
Alerts visible directly on Grafana dashboard
Monitoring pipeline enhanced with alerting capability


### Conclusion

Alerting adds an automated layer of observability by detecting issues without manual monitoring. By combining Mtail logs parsed into prometheus compatible metrics with Alert rules and Grafana visualization, the system can proactively identify and respond to potential problems in the application.

# Day-8 Gitlab CI/CD Integration

## Objective

The goal for today involves automation using CI/CD pipelines

- Building Gitlab CI/CD pipeline
- Setting up Gitlab local runner so we pipeline can get access to our cluster(minikube)

## Components Used
- Gitlab : to build CI/CD pipeline
- local Runner: configuring local runner for pipeline

## Pipeline Flow
developer -> git push  
         |  
  pipeline runs  
         |  
  Uses kubectl inside pipeline
         |  
  Applies configuration to : apache, nginx, monitoring  
         |  
  Kubernetes updated automatically
  
## Configuration

### step:1 Write .gitlab-ci.yaml

- This file tells GitLab what to do when you push code.
- You define stages like:
 - build
 - test
 - deploy
- Each stage contains steps (jobs) GitLab should run automatically.

### Step:2 Setup gitlab local runner

- A runner is a machine that actually executes pipeline jobs.
- Install Gitlab runner on local system.
- Connect (register) it with gitlab project using token
- Choose how it runs jobs (here it was shell)

### Step:3 Set gitlab as remote repo

- Local project needs to know where to push code.
- Add gitlab repo URL as remote.
- and also add githu url as github(backup)
- after that, git push will send code to gitlab and github
```
git config --global alias.pushall '!git push origin main && git push github main'
git pushall
```

### Step:4 Setup SSH key to push without password or token (optional)

- Normally Git asks for username/password or token when pushing.
- SSH keys remove that by creating a secure trust between your system and GitLab.

How it works:

- Generate an SSH key on your system
- Copy the public key
- Add it to your GitLab account
- Use the SSH repo URL instead of HTTPS
After this:
- You can push/pull without entering password every time

## Output

A fully functional pipeline:
- Triggers on every push
- updates kubernetes automatically

## Result

- Pipeline successfully configured
- changes made and pushed to gitlab
- pipeline triggers and rollout deployment done

## Conclusion

- Setup a whole pipeline which connects to minikube cluster , verifies the nodes , applies the configuration files and rollouts and restarts the deployment in kubernetes automatically . Firstly, after every minor change we manually fired kubectl commands, N			ow after every change we just need to push code and every little change is applied automatically 
