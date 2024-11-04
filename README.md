# Complete Production E2E Pipeline
This project demonstrates a complete end-to-end (E2E) CI/CD pipeline to deploy Java application on a kubernetes cluster using Jenkins for CI and ArgoCD for CD part. The setup is designed for deploying and managing a production-grade Java application on a Kubernetes cluster with automated testing, containerization, SonarQube code analysis, and ArgoCD for continuous deployment.

---

## Prerequisites

Ensure you have the following infrastructure and tools set up:

- **Kubernetes Cluster**: A running Kubernetes cluster.
- **Jenkins**: Installed and configured with required plugins.
- **ArgoCD**: Installed on the Kubernetes cluster for deployment management or the kubernetes cluster is configured as cluster for deploying applications.
- **SonarQube**: Running and accessible for code quality analysis.

---

## Project Structure

- **Jenkinsfile**: Defines the CI pipeline stages, from building the application to pushing a Docker image and update the deployment files with new pushed image.
- **deployment/**: Contains Kubernetes manifests for the application deployment and service.
- **argocd-app.yaml**: Defines the ArgoCD application resource.

---

## Pipeline Overview

The pipeline is structured into multiple stages:

1. **Cleanup Workspace**: Cleans up the Jenkins workspace to ensure no leftover artifacts.
2. **Git Checkout**: Checks out the latest code from the main branch.
3. **Build Application**: Builds the Java application using Maven.
4. **Test Application**: Runs the unit tests using Maven.
5. **SonarQube Analysis**: Performs a static code analysis using SonarQube.
6. **Quality Gate**: Enforces SonarQube quality gate checks.
7. **Build & Push Docker Image**: Builds a Docker image of the application and pushes it to Docker Hub.
8. **Cleanup Artifacts**: Removes the local Docker images to save space.
9. **Update Deployment File**: Updates the Kubernetes deployment YAML with the new image version and pushes the change to GitHub.

---