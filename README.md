# Kubernetes-Pods

![Kubernetes](https://img.shields.io/badge/kubernetes-%23326ce5.svg?style=for-the-badge&logo=kubernetes&logoColor=white)
![Docker](https://img.shields.io/badge/docker-%230db7ed.svg?style=for-the-badge&logo=docker&logoColor=white)
![YAML](https://img.shields.io/badge/yaml-%23ffffff.svg?style=for-the-badge&logo=yaml&logoColor=151515)

A comprehensive collection of Kubernetes configuration files and manifests for learning and practicing Kubernetes concepts. This repository demonstrates hands-on experience with container orchestration, including Pods, ConfigMaps, Persistent Volume Claims (PVCs), Services, and StatefulSets.

## 📋 Table of Contents

- [Overview](#overview)
- [Repository Structure](#repository-structure)
- [Prerequisites](#prerequisites)
- [Getting Started](#getting-started)
- [Components](#components)
- [Usage Examples](#usage-examples)
- [Learning Objectives](#learning-objectives)
- [Contributing](#contributing)
- [License](#license)

## 🎯 Overview

This repository contains practical Kubernetes YAML manifests organized by functionality. Each component demonstrates real-world use cases and best practices for deploying applications on Kubernetes.

**Key Features:**
- Production-ready YAML configurations
- Organized by Kubernetes resource types
- Hands-on examples for learning
- Best practices implementation
- Ready to deploy on Minikube or any K8s cluster

## 📁 Repository Structure

```
Kubernetes-Pods/
├── Kubernetes/
│   ├── configmap/          # ConfigMap configurations
│   ├── pvc/                # Persistent Volume Claims
│   ├── service.demo/       # Service definitions
│   └── statefulset/        # StatefulSet manifests
└── README.md
```

### Components Breakdown:

- **configmap/**: Configuration data management for applications
- **pvc/**: Storage management using Persistent Volume Claims
- **service.demo/**: Service networking and load balancing
- **statefulset/**: Stateful application deployments

## ⚙️ Prerequisites

Before you begin, ensure you have the following installed:

- **Kubernetes Cluster**: Minikube, Kind, or any K8s cluster
- **kubectl**: Kubernetes command-line tool
- **Docker**: Container runtime (for Minikube)

### Installation Links:

```bash
# Install kubectl
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"

# Install Minikube
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube

# Start Minikube
minikube start
```

## 🚀 Getting Started

### 1. Clone the Repository

```bash
git clone https://github.com/Sheersh123/Kubernetes-Pods.git
cd Kubernetes-Pods
```

### 2. Verify Cluster Connection

```bash
kubectl cluster-info
kubectl get nodes
```

### 3. Navigate to Specific Component

```bash
cd Kubernetes/<component-name>
```

## 🔧 Components

### ConfigMaps

ConfigMaps allow you to decouple configuration artifacts from container images.

```bash
cd Kubernetes/configmap
kubectl apply -f <configmap-file>.yaml
kubectl get configmaps
```

**Use Cases:**
- Application configuration
- Environment variables
- Command-line arguments

### Persistent Volume Claims (PVC)

PVCs provide persistent storage for stateful applications.

```bash
cd Kubernetes/pvc
kubectl apply -f <pvc-file>.yaml
kubectl get pvc
kubectl get pv
```

**Use Cases:**
- Database storage
- File uploads
- Application state persistence

### Services

Services enable network access to pods and load balancing.

```bash
cd Kubernetes/service.demo
kubectl apply -f <service-file>.yaml
kubectl get services
```

**Service Types:**
- ClusterIP (default)
- NodePort
- LoadBalancer

### StatefulSets

StatefulSets manage stateful applications with stable network identities.

```bash
cd Kubernetes/statefulset
kubectl apply -f <statefulset-file>.yaml
kubectl get statefulsets
kubectl get pods
```

**Use Cases:**
- Databases (MySQL, MongoDB, PostgreSQL)
- Message queues (Kafka, RabbitMQ)
- Distributed systems requiring stable identities

## 💡 Usage Examples

### Deploy All Resources

```bash
# Apply all configurations
kubectl apply -f Kubernetes/ --recursive

# Verify deployments
kubectl get all
```

### Check Resource Status

```bash
# View pods
kubectl get pods -o wide

# View services
kubectl get svc

# Describe a resource
kubectl describe pod <pod-name>

# View logs
kubectl logs <pod-name>
```

### Clean Up Resources

```bash
# Delete specific resource
kubectl delete -f <file>.yaml

# Delete all resources in directory
kubectl delete -f Kubernetes/ --recursive
```

## 📚 Learning Objectives

By working with this repository, you will learn:

✅ **Kubernetes Fundamentals**
- Pod lifecycle management
- Resource configuration with YAML
- Kubernetes object model

✅ **Configuration Management**
- Using ConfigMaps for application config
- Managing secrets (best practices)
- Environment variable injection

✅ **Storage Management**
- Understanding Persistent Volumes
- Working with Persistent Volume Claims
- Storage classes and provisioning

✅ **Networking**
- Service types and their use cases
- Service discovery
- Load balancing strategies

✅ **Stateful Applications**
- StatefulSet vs Deployment
- Stable network identities
- Ordered deployment and scaling

## 🤝 Contributing

Contributions are welcome! If you'd like to improve this repository:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/improvement`)
3. Commit your changes (`git commit -m 'Add some improvement'`)
4. Push to the branch (`git push origin feature/improvement`)
5. Open a Pull Request

## 📝 License

This project is open source and available under the [MIT License](LICENSE).

## 📧 Contact

**Sheersh Tiwari**
- GitHub: [@Sheersh123](https://github.com/Sheersh123)
- Repository: [Kubernetes-Pods](https://github.com/Sheersh123/Kubernetes-Pods)

---

⭐ If you found this repository helpful, please consider giving it a star!

**Tags:** `kubernetes` `devops` `containers` `docker` `k8s` `kubectl` `configmap` `statefulset` `pvc` `services` `cloud-native` `orchestration`
