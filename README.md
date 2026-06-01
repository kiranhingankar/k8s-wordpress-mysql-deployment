# Kubernetes WordPress + MySQL Deployment

A complete Kubernetes capstone project that deploys a highly available WordPress application backed by a persistent MySQL database using core Kubernetes concepts including Namespaces, Secrets, ConfigMaps, StatefulSets, Deployments, Services, Persistent Volumes, Resource Management, Health Probes, and Horizontal Pod Autoscaling (HPA).

---

## Project Overview

This repository demonstrates how to deploy a production-style WordPress and MySQL application stack on Kubernetes.

The deployment includes:

* Dedicated Namespace
* MySQL StatefulSet
* Headless Service for MySQL
* Persistent Storage using PVCs
* Kubernetes Secrets for credentials
* ConfigMaps for application configuration
* WordPress Deployment with multiple replicas
* Resource Requests and Limits
* Liveness and Readiness Probes
* NodePort Service for external access
* Horizontal Pod Autoscaler (HPA)
* Self-Healing Verification
* Data Persistence Verification

---

## Architecture

```text
User Browser
      │
      ▼
NodePort Service (30080)
      │
      ▼
WordPress Deployment (2 Replicas)
      │
      ├── ConfigMap
      ├── Secret
      │
      ▼
Headless Service (mysql-svc)
      │
      ▼
MySQL StatefulSet
      │
      ▼
Persistent Volume Claim
```

---

## Kubernetes Concepts Used

| Concept                 | Purpose                          |
| ----------------------- | -------------------------------- |
| Namespace               | Resource isolation               |
| Secret                  | Store MySQL credentials          |
| ConfigMap               | Store WordPress configuration    |
| StatefulSet             | Stable MySQL deployment          |
| Deployment              | Manage WordPress replicas        |
| Headless Service        | StatefulSet DNS resolution       |
| NodePort Service        | External access to WordPress     |
| Persistent Volume Claim | Database persistence             |
| Resource Requests       | Guaranteed resources             |
| Resource Limits         | Prevent resource overconsumption |
| Readiness Probe         | Verify pod readiness             |
| Liveness Probe          | Detect unhealthy containers      |
| HPA                     | Automatic scaling                |

---

## Project Structure

```text
k8s-wordpress-mysql-deployment/
│
├── manifests/
│   ├── namespace.yaml
│   ├── mysql-secret.yaml
│   ├── mysql-headless-service.yaml
│   ├── mysql-statefulset.yaml
│   ├── wordpress-configmap.yaml
│   ├── wordpress-deployment.yaml
│   ├── wordpress-service.yaml
│   └── wordpress-hpa.yaml
│
├── screenshots/
│   └── wordpress-blog-post.png
│
└── README.md
```

---

## Namespace

```text
capstone-ns
```

---

## MySQL Configuration

### Service Name

```text
mysql-svc
```

### Database

```text
wordpress
```

### Deployment Type

```text
StatefulSet
```

### Storage

```text
1Gi Persistent Volume Claim
```

---

## WordPress Configuration

### Deployment Type

```text
Deployment
```

### Replicas

```text
2
```

### Service Type

```text
NodePort
```

### NodePort

```text
30080
```

---

## Deployment Workflow

### 1. Create Namespace

```bash
kubectl apply -f manifests/namespace.yaml
kubectl config set-context --current --namespace=capstone-ns
```

### 2. Create Secret

```bash
kubectl apply -f manifests/mysql-secret.yaml
```

### 3. Deploy MySQL

```bash
kubectl apply -f manifests/mysql-headless-service.yaml
kubectl apply -f manifests/mysql-statefulset.yaml
```

### 4. Verify MySQL

```bash
kubectl get pods
kubectl get pvc
```

### 5. Deploy WordPress

```bash
kubectl apply -f manifests/wordpress-configmap.yaml
kubectl apply -f manifests/wordpress-deployment.yaml
kubectl apply -f manifests/wordpress-service.yaml
```

### 6. Access WordPress

#### Minikube

```bash
minikube service wordpress-service -n capstone-ns
```

#### Kind

```bash
kubectl port-forward svc/wordpress-service 80:80 -n capstone-ns
```

Open:

```text
http://localhost:80
```

---

## Horizontal Pod Autoscaler

Apply HPA:

```bash
kubectl apply -f manifests/wordpress-hpa.yaml
```

Verify:

```bash
kubectl get hpa -n capstone-ns
```

Configuration:

| Setting      | Value |
| ------------ | ----- |
| Min Replicas | 2     |
| Max Replicas | 10    |
| CPU Target   | 50%   |

---

## Self-Healing Test

Delete a WordPress pod:

```bash
kubectl delete pod <wordpress-pod-name>
```

Expected Result:

* Pod automatically recreated by Deployment
* Website remains available

Delete MySQL pod:

```bash
kubectl delete pod mysql-statefulset-0
```

Expected Result:

* StatefulSet recreates pod
* Database data remains intact

---

## Persistence Test

1. Create a WordPress blog post
2. Delete MySQL pod
3. Wait for MySQL recovery
4. Refresh WordPress

Expected Result:

* Blog post still exists
* Data preserved through PVC

---

## Verification Commands

### View All Resources

```bash
kubectl get all -n capstone-ns
```

### View PVCs

```bash
kubectl get pvc -n capstone-ns
```

### View Services

```bash
kubectl get svc -n capstone-ns
```

### View Pods

```bash
kubectl get pods -n capstone-ns
```

### View HPA

```bash
kubectl get hpa -n capstone-ns
```

---

## Learning Outcomes

This project combines multiple Kubernetes concepts into a single real-world application deployment and demonstrates:

* Container Orchestration
* Stateful Application Management
* Persistent Storage
* Application Configuration
* Secret Management
* Health Monitoring
* Auto Scaling
* Service Discovery
* Self-Healing Workloads

---

## Author

Kiran Hingankar
