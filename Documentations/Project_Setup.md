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

<div>

[]{#anchor}*kubectl apply -f k8s/nginx/deployment.yaml*

*kubectl apply -f k8s/nginx/service.yaml*

*kubectl apply -f k8s/apache/deployment.yaml*

*kubectl apply -f k8s/apache/service.yaml*

</div>

### Verify Running Pods

<div>

[]{#anchor-1}*kubectl get pods -n demo-apps*

</div>

### Verify Services

<div>

[]{#anchor-2}*kubectl get svc -n demo-apps*

</div>

### Access Services

<div>

[]{#anchor-3}*minikube service nginx-service -n demo-apps*

*minikube service apache-service -n demo-apps*

</div>
