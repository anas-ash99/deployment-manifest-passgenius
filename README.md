# Deployment Manifests - PassGenius

Kubernetes deployment manifests and configurations for the PassGenius application. This project uses [Kustomize](https://kustomize.io/) for configuration management.

## Architecture

The application is deployed as a set of microservices on Kubernetes. The configuration is organized using Kustomize overlays to support different environments (currently `dev`).

### Components

The deployment includes the following components:

*   **Services**:
    *   `auth`: Authentication service.
    *   `user`: User management service.
*   **Infrastructure**:
    *   `mongodb`: Database for storing application data.
    *   `kong`: API Gateway for managing traffic to services.
    *   `ingress`: Ingress resources for external access.

### Directory Structure

*   `overlays/dev`: Contains the Kustomize configuration for the development environment.
    *   `kustomization.yaml`: The entry point for the dev overlay, aggregating all resources.
    *   `auth/`, `user/`, `mongodb/`, `kong/`, `ingress/`: Directory for each component containing its specific Kubernetes manifests (Deployment, Service, etc.).
