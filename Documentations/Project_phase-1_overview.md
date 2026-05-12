# Project Title

Kubernetes Monitoring Locally

# Project Overview

This project focuses on building a containerized application environment
using Kubernetes on a local system with Minikube. Multiple demo
applications such as Nginx and Apache will be deployed inside the
Kubernetes cluster to simulate real-world workloads.

To monitor the performance and health of these applications, a complete
monitoring stack consisting of Prometheus, Grafana, and Alertmanager
will be integrated into the cluster. The monitoring system will collect
and visualize important metrics such as CPU usage, memory consumption,
pod status, and overall cluster health.

The project will also implement a CI/CD pipeline using Git and GitLab
CI/CD to automate application deployment. Whenever changes are pushed to
the GitLab repository, the pipeline will automatically build, validate,
and deploy the updated configurations to the Kubernetes cluster.

Additionally, alerting mechanisms will be configured to notify when
system thresholds such as high CPU or memory usage are exceeded.

# Objectives

-   Deploy containerized applications on Kubernetes
-   Implement cluster monitoring using Prometheus and Grafana
-   Configure automated alerting with Alertmanager
-   Automate deployment using GitLab CI/CD
-   Simulate a real-world DevOps workflow locally
-   Prepare the project for future migration to AWS EKS

# Technologies Used

1.  **Containerization**

    1.  Docker

2.  **Container Orchestration**

    1.  Kubernetes
    2.  Minikube

3.  **Monitoring and Alerting**

    1.  Prometheus
    2.  Grafana
    3.  Loki

4.  **CI/CD**

    1.  Gitlab

5.  **Demo Server**

    1.  Nginx
    2.  Apache

# Key Features

-   Kubernetes-based application deployment
-   Multi-container environment
-   Real-time monitoring dashboards
-   CPU and memory usage tracking
-   Automated alert generation
-   CI/CD deployment automation
-   Scalable and cloud-ready architecture

# Project Workflow

-   Developer pushes code/configuration changes to GitLab repository
-   GitLab CI/CD pipeline gets triggered automatically
-   Docker images are built and deployed to Kubernetes
-   Prometheus collects cluster and application metrics
-   Grafana visualizes metrics through dashboards
-   Alertmanager sends alerts based on defined rules

# Future Enhancements

-   Migration to AWS EKS
-   Horizontal Pod Autoscaling
-   SSL/TLS configuration
-   Advanced application monitoring

# Expected Outcome

By the completion of this project, a fully functional local Kubernetes
environment with monitoring, alerting, and CI/CD automation will be
successfully implemented, demonstrating core DevOps and cloud-native
concepts.
