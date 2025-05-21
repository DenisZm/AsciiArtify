# Minimum Viable Product (MVP) for AsciiArtify with ArgoCD

This document describes the process of deploying the `demo` application using ArgoCD to demonstrate the GitOps approach for the AsciiArtify project. It includes instructions for setting up the application via the ArgoCD UI with manual synchronization and accessing the deployed application, along with references to demo videos hosted on YouTube and a terminal recording on asciinema.org.

## Prerequisites
- A running Kubernetes cluster with ArgoCD installed and configured.
- Access to the ArgoCD UI.
- Installed `kubectl` tool.

## Steps for Deployment

### 1. Create an ArgoCD Application via UI
1. Open the ArgoCD UI:
   - Ensure the ArgoCD server is accessible by setting up port-forwarding:
     ```bash
     kubectl port-forward svc/argocd-server -n argocd 8080:443
     ```
   - Navigate to `https://localhost:8080`.
   - Log in with:
     - **Username**: `admin`
     - **Password**: Retrieved via `kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d`
2. Create a new application:
   - Click **"New App"** in the ArgoCD UI.
   - Fill in the application details:
     - **Application Name**: `demo`
     - **Project**: `default`
     - **Sync Policy**: Select **Manual**.
     - **Sync Options**: Check **Auto-create namespace** and **Server-side Apply**.
     - **Repository URL**: `https://github.com/den-vasyliev/go-demo-app.git`
     - **Path**: `helm`
     - **Cluster URL**: `https://kubernetes.default.svc`
     - **Namespace**: `demo`
   - Click **"Create"** to save the application.
3. Verify the application creation and synchronization:
   - In the ArgoCD UI, check that the `demo` application appears.
   - Manually sync the application by clicking **"Sync"** in the UI to deploy the resources from the Git repository.
   - Ensure the application is in the `Synced` and `Healthy` state.
   - **Demo Video**: The process of creating the application and its manual synchronization is demonstrated in the following video:  
     [![Application Creation and Synchronization Demo](https://img.youtube.com/vi/acMqcRXcnso/0.jpg)](https://www.youtube.com/watch?v=acMqcRXcnso)

### 2. Access the Deployed Application
1. Check the deployed resources:
   ```bash
   kubectl get pods -n demo
   kubectl get svc -n demo
   ```
   The `demo` application exposes a service named `demo-api` on port `80` (check the `helm/templates/service.yaml` in the repository for details).
2. Set up port-forwarding to access the application:
   ```bash
   kubectl port-forward -n demo svc/demo-api 8088:80
   ```
3. Open a browser and navigate to `http://localhost:8088` to access the `demo` application.
4. Verify the application functionality:
   - Test the application by uploading an image to the `/img/` endpoint:
     ```bash
     wget -O /tmp/g.png https://www.google.com/images/branding/googlelogo/1x/googlelogo_color_272x92dp.png
     curl -F 'image=@/tmp/g.png' http://localhost:8088/img/
     ```
   - **Terminal Recording**: The process of accessing and testing the application is demonstrated in the following asciinema recording:  
     [![asciicast](https://asciinema.org/a/PaYxNBGDbejMWhJ3nfiSg6K4l.svg)](https://asciinema.org/a/PaYxNBGDbejMWhJ3nfiSg6K4l)

## Conclusion
This MVP demonstrates the deployment of the `demo` application using ArgoCD with manual synchronization enabled. The YouTube demo videos and asciinema recording showcase the application creation, manual synchronization, and functionality of the deployed application.