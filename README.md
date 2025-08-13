# DO_Nodejs_Web_App

🚀 **SaaS Web Application Deployment on Kubernetes – Case Study ( Digital Ocean )**

📌 Overview

This project demonstrates the end-to-end deployment of a containerized web application for a SaaS company, aligning with their strategic priorities:

    1. Scalability – Handle increased workloads as the user base grows.
    2. Performance – Maintain fast response times even under load.
    3. Reliability – Deliver high availability with minimal downtime.
    4. Cost Optimization – Use cloud resources efficiently without sacrificing  performance.

The deployment leverages Docker, DigitalOcean K8's ,Load Balancer and Horizontal Pod Autoscaling.


📁 Project Structure

.
├── index.html           # Static webpage
├── Dockerfile           # Docker build instructions
├── deployment.yaml      # Kubernetes Deployment manifest
├── service.yaml         # Kubernetes LoadBalancer Service manifest




📋 **Prerequisites**

    1. DigitalOcean Account – [Sign up](https://www.digitalocean.com/)
    2. Docker – Installed & running locally
    3. Docker Hub Account – For hosting container images
    4. doctl – DigitalOcean
    5. kubectl – Kubernetes CLI
    6. Metrics Server – Installed on the Kubernetes cluster


📦 Step-by-Step Deployment Guide

    1️. Clone the Repository

    `git clone https://github.com/reeteshksingh31/DO_Nodejs_Web_App.git
    cd DO_Nodejs_Web_App`

     Build & Push Docker Image

    # Build Docker image
    docker build -t reeteshsingh31/nodejs:newimage .

    # Push to Docker Hub
    docker push reeteshsingh31/nodejs:newimage
    

    2️⃣ Create Kubernetes Cluster on DigitalOcean & Authenticate

    1. Create a Kubernetes Cluster via [DigitalOcean Control Panel]
        Region: Closest to user base
        Node Pool: 2 nodes (auto-scale enabled: min 2, max 3)
        Kubernetes version: Latest stable

    2. Generate DigitalOcean API Token:
        Go to: **Account → Applications & API → Generate New Token
        Copy the generated token and store it safely.

    3. Authenticate with doctl:

        doctl auth init [ Paste your API token when prompted ]

    4. Connect to your cluster:

        doctl kubernetes cluster kubeconfig save <cluster_name>

    5. Verify connection:

        kubectl get nodes


    3️⃣ Deploy Application

    # Create deployment
      kubectl apply -f deployment.yaml

    # Verify pods
      kubectl get pods


    4️⃣ Create LoadBalancer Service

    kubectl apply -f lbservice.yaml

    # Get external IP
    kubectl get svc <lb_service_name>

Once the external IP appears, you can access the app in a browser.


    5️⃣ Enable Horizontal Pod Autoscaling

    # Ensure Metrics Server is running
    kubectl get deployment metrics-server -n kube-system

    # Apply HPA
    kubectl apply -f hpa.yaml

    # Monitor scaling
    kubectl get hpa


📈 **Validation & Testing**

    1. Load Testing (BusyBox Method)

    kubectl run -it --rm load-generator --image=busybox --restart=Never -- /bin/sh -c "while true; do wget -q -O- http://nodejs-lb; done"

> Replace `nodejs-lb` with your LoadBalancer service name.

    2. Failover Test

    kubectl delete pod <pod_name>
    kubectl get pods

    Observe Kubernetes automatically recreating the pod.





