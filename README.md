# Dual Flask App Kubernetes Deployment

A production-ready Kubernetes deployment for two Flask applications using Kustomize for environment-specific configurations.

## 🚀 Project Overview

This project demonstrates a complete Kubernetes setup for deploying two Flask applications:
- **App 1**: Returns "Hello World!" (accessible at `/demo`)
- **App 2**: Returns "Hello from App2!" (accessible at `/demo2`)

## 📁 Project Structure

```
demo/
├── base/                          # Common Kubernetes resources
│   ├── kustomization.yaml         # Base Kustomize configuration
│   ├── deployments.yaml           # Common deployment config
│   ├── services.yaml              # Common service config
│   ├── ingress.yaml               # Common ingress config
│   └── namespaces.yaml            # Common namespace config
├── overlays/
│   ├── dev/                       # Development environment
│   │   ├── kustomization.yaml     # Dev-specific config
│   │   ├── demo-app-patch.yaml    # Adds imagePullPolicy: Never
│   │   └── demo2-app-patch.yaml
│   └── prod/                      # Production environment
│       ├── kustomization.yaml     # Prod-specific config
│       ├── demo-app-patch.yaml    # Adds imagePullPolicy: Always
│       └── demo2-app-patch.yaml
├── app.py                         # Flask App 1
├── app2.py                        # Flask App 2
├── requirements.txt               # Python dependencies
├── Dockerfile                     # Dockerfile for App 1
└── Dockerfile.app2                # Dockerfile for App 2
```

## 🛠️ Prerequisites

- Docker
- Kubernetes cluster (Minikube, kind, or cloud cluster)
- kubectl
- Ingress controller (NGINX Ingress for Minikube)

## 🚀 Quick Start

### 1. Build Docker Images

```bash
# Build images for development
docker build -t demo-app:latest .
docker build -f Dockerfile.app2 -t demo2-app:latest .

# For Minikube (use Minikube's Docker daemon)
eval $(minikube docker-env)
docker build -t demo-app:latest .
docker build -f Dockerfile.app2 -t demo2-app:latest .
```

### 2. Enable Ingress Controller (Minikube)

```bash
minikube addons enable ingress
```

### 3. Deploy Applications

```bash
# Development deployment
kubectl apply -k overlays/dev/

# Check status
kubectl get all -n demo-apps
```

### 4. Access Applications

```bash
# Start port forwarding
kubectl port-forward -n ingress-nginx service/ingress-nginx-controller 8080:80

# Test applications
curl http://localhost:8080/demo/     # "Hello World!"
curl http://localhost:8080/demo2/    # "Hello from App2!"
```

## 🌍 Environment Configurations

### Development Environment
- Uses local Docker images
- `imagePullPolicy: Never`
- Perfect for local development

### Production Environment
- Uses registry images
- `imagePullPolicy: Always`
- Production-ready configuration

```bash
# Preview production configuration
kubectl kustomize overlays/prod/

# Deploy to production (after pushing images to registry)
kubectl apply -k overlays/prod/
```

## 📊 Monitoring & Management

### Check Application Status
```bash
# Check all resources
kubectl get all -n demo-apps

# Check pod status
kubectl get pods -n demo-apps

# Check deployment status
kubectl get deployments -n demo-apps
```

### View Logs
```bash
# View application logs
kubectl logs -n demo-apps deployment/demo-app
kubectl logs -n demo-apps deployment/demo2-app
```

### Restart Applications
```bash
# Restart deployments
kubectl rollout restart deployment/demo-app -n demo-apps
kubectl rollout restart deployment/demo2-app -n demo-apps

# Check rollout status
kubectl rollout status deployment/demo-app -n demo-apps
```

## 🔧 Configuration Details

### High Availability
- 3 replicas per application
- Health checks (readiness and liveness probes)
- Resource limits and requests

### Networking
- ClusterIP services for internal communication
- NGINX Ingress for external access
- URL routing: `/demo` → App 1, `/demo2` → App 2

### Resource Management
- CPU: 100m request, 250m limit
- Memory: 128Mi request, 256Mi limit

## 🧹 Cleanup

```bash
# Remove all resources
kubectl delete -k overlays/dev/

# Or remove specific resources
kubectl delete namespace demo-apps
```

## 📝 Key Features

- ✅ **Kustomize-based configuration management**
- ✅ **Environment-specific deployments**
- ✅ **High availability (3 replicas)**
- ✅ **Health checks and monitoring**
- ✅ **Resource limits and requests**
- ✅ **Ingress-based routing**
- ✅ **Production-ready setup**

## 🤝 Contributing

1. Create a feature branch
2. Make your changes
3. Test with development overlay
4. Create a pull request

## 📄 License

This project is open source and available under the MIT License. 