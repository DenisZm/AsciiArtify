# Proof of Concept (PoC) for AsciiArtify with ArgoCD

This document outlines the process of deploying ArgoCD in a Kubernetes cluster to demonstrate the GitOps approach for the AsciiArtify project.

## Prerequisites
- Installed tools:
  - kubectl
- A running Kubernetes cluster created with k3d in GitHub Codespaces.

## Steps for Deployment

### 1. Install ArgoCD
1. Create the `argocd` namespace:
   ```bash
   kubectl create namespace argocd
   ```
2. Install ArgoCD using the official manifests:
   ```bash
   kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
   ```
3. Verify that all pods are running:
   ```bash
   kubectl get pods -n argocd
   ```

### 2. Access the ArgoCD UI
1. Retrieve the admin password:
   ```bash
   kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
   ```
   Save the password (e.g., `7aX9k2mL3pQ8zW5v`).
2. Set up port-forwarding to access the UI:
   ```bash
   kubectl port-forward svc/argocd-server -n argocd 8080:443
   ```
3. Open a browser and navigate to: `https://localhost:8080`
   - **Username**: `admin`
   - **Password**: The password obtained in the previous step.

### 3. Access the ArgoCD UI in GitHub Codespaces
In GitHub Codespaces, direct access to `localhost` is restricted due to the isolated environment. Follow these steps to access the ArgoCD UI:
1. Ensure the port-forward command is running:
   ```bash
   kubectl port-forward svc/argocd-server -n argocd 8080:443
   ```
2. In the GitHub Codespaces interface, go to the **"Ports"** tab.
3. Locate port `8080` in the list (it should appear after running the port-forward command).
4. Enable HTTPS for the port:
   - Right-click on port `8080` in the "Ports" tab.
   - Select **"Port Protocol"** and choose **"HTTPS"** to ensure the port uses a secure connection.
5. Set the port visibility to **"Public"** by clicking the visibility icon or right-clicking the port and selecting "Make Public".
6. Copy the generated public URL for port `8080` (e.g., `https://<codespace-name>-8080.app.github.dev`).
7. Open the URL in a browser and log in to the ArgoCD UI:
   - **Username**: `admin`
   - **Password**: The password obtained in step 2.1.
   - **Note**: If you encounter SSL certificate errors due to ArgoCD's self-signed certificate, you may need to accept the certificate in your browser to proceed.

### 4. Verify Functionality
- Log in to the ArgoCD UI using the public URL from GitHub Codespaces.
- Ensure the interface displays the cluster status.


## Conclusion
This PoC demonstrates the feasibility of deploying ArgoCD in a Kubernetes cluster to implement a GitOps approach. The system is ready for further integration with the AsciiArtify project to automate deployments.
