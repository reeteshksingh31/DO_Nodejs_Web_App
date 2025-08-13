# DO_Nodejs_Web_App

🚀 **Web Application Deployment on DigitalOcean Kubernetes**

---

## 📌 Overview

This repository contains NodeJs containerized web application deployment on Digital Ocean Kubernetes cluster(DOKS).

This Deployment leverages

```md

- ✅ Docker-based containerization.  
- ✅ Kubernetes deployment and service manifests  
- ✅ LoadBalancer to expose the app publicly.  
- ✅ Horizontal Pod Autoscaler (HPA) – Automatically scales pods based on CPU usage.  
- ✅ Cluster autoscaling configured during cluster creation (min 2, max 3 nodes)  
```

---

## 📁 Project Structure

```plaintext
.
├── app.js               # NodeJs App
├── package.json         # NodeJs app dependency file
├── Dockerfile           # Docker build instructions
├── deployment.yaml      # Kubernetes Deployment manifest
├── service.yaml         # Kubernetes LoadBalancer Service manifest
├── hpa.yaml             # Horizontal Pod Autoscaler manifest
````

---

## 📋 Prerequisites

* [DigitalOcean Account](https://www.digitalocean.com/)
* Docker (installed & running locally)
* Docker Hub Account (for hosting container images)
* `doctl` – DigitalOcean CLI
* `kubectl` – Kubernetes CLI
* Metrics Server installed on the Kubernetes cluster

---

## 📦 Step-by-Step Deployment Guide

### **1️⃣ Clone the Repository**

```bash
git clone https://github.com/reeteshksingh31/DO_Nodejs_Web_App.git
cd DO_Nodejs_Web_App
```

### **2️⃣ Build & Push Docker Image**

```bash
docker build -t reeteshsingh31/nodejs:newimage .
docker push reeteshsingh31/nodejs:newimage
```

### **3️⃣ Create Kubernetes Cluster on DigitalOcean & Authenticate**

Create the cluster with autoscaling enabled:

```bash
doctl kubernetes cluster create web-app-cluster \
  --region <region> \
  --version <k8s-version> \
  --min-nodes 2 \
  --max-nodes 3 \
  --count 2 \
  --enable-autoscaling
```

Authenticate with `doctl`:

```bash
doctl auth init
```

Save kubeconfig:

```bash
doctl kubernetes cluster kubeconfig save <cluster-name>
```

Verify cluster access:

```bash
kubectl get nodes
```

### **4️⃣ Deploy Application**

```bash
kubectl apply -f deployment.yaml
kubectl get pods
```

### **5️⃣ Create LoadBalancer Service**

```bash
kubectl apply -f lbservice.yaml

# Get external IP
kubectl get svc <lb_service_name>
```

Once the **external IP** appears, you can access the app in a browser.

### **6️⃣ Enable Horizontal Pod Autoscaling**

```bash
# Ensure Metrics Server is running
kubectl get deployment metrics-server -n kube-system
kubectl apply -f hpa.yaml

```

---

## 📈 Validation & Testing

### **Load Testing (BusyBox Method)**

```bash
kubectl run -it --rm load-generator \
  --image=busybox \
  --restart=Never \
  -- /bin/sh -c "while true; do wget -q -O- http://<load balancer service name>; done"
```
