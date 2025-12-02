# Kubernetes-Pods

A curated collection of Kubernetes manifests, examples, and learning experiments focused on Pods and other basic workload primitives. This repository is intended for developers and learners who want hands-on examples that demonstrate core Kubernetes concepts using minimal, easy-to-read YAML.

Contents include:
- simple Pod manifests for single-container workloads
- Deployments and ReplicaSets showing rolling updates and scaling
- Service examples (ClusterIP, NodePort, LoadBalancer)
- Practical tips for debugging (kubectl exec, logs, port-forward)
- Scripts and helper manifests used for demonstrations

---

Table of Contents
- [Overview](#overview)
- [Repository layout](#repository-layout)
- [Prerequisites](#prerequisites)
- [Quick start examples](#quick-start-examples)
  - [Create a simple Pod](#create-a-simple-pod)
  - [Create a Deployment and scale](#create-a-deployment-and-scale)
  - [Expose a Deployment with a Service](#expose-a-deployment-with-a-service)
- [Debugging & common commands](#debugging--common-commands)
- [Best practices & notes](#best-practices--notes)
- [Testing locally](#testing-locally)
- [Contributing](#contributing)
- [License](#license)
- [Contact](#contact)

---

## Overview

This repo is intentionally small and practical — each manifest demonstrates one concept with minimal extra YAML. Use it to practice kubectl commands, to teach others, or to reference for small demos and experiments.

---

## Repository layout

The repository organizes Kubernetes examples under the top-level Kubernetes directory. Adjust paths below if you add new folders.

Typical directory structure:

- Kubernetes/
  - pods/            # single pod examples
  - deployments/     # Deployment and ReplicaSet examples
  - services/        # Service examples (ClusterIP, NodePort, LoadBalancer)
  - init-containers/ # examples using init containers
  - volumes/         # examples using emptyDir, hostPath, PVC
- scripts/           # helper scripts (local cluster creation, cleanup)
- README.md
- LICENSE

If you add new folders, update this README to reflect the new structure.

---

## Prerequisites

- kubectl (v1.20+ recommended)
- A Kubernetes cluster: Minikube, kind, k3d, microk8s, or an existing cloud cluster
- (Optional) docker or podman to build images locally

---

## Quick start examples

All commands assume your kubectl context is set to the cluster where you want to run these examples.

### Create a simple Pod

Save this as Kubernetes/pods/nginx-pod.yaml:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-demo
  labels:
    app: nginx-demo
spec:
  containers:
  - name: nginx
    image: nginx:stable
    ports:
    - containerPort: 80
```

Apply:

```bash
kubectl apply -f Kubernetes/pods/nginx-pod.yaml
kubectl get pods -l app=nginx-demo
```

Port-forward to test in your browser:

```bash
kubectl port-forward pod/nginx-demo 8080:80
# open http://localhost:8080
```

### Create a Deployment and scale

Deployment manifest (Kubernetes/deployments/nginx-deployment.yaml):

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx-deploy
  template:
    metadata:
      labels:
        app: nginx-deploy
    spec:
      containers:
      - name: nginx
        image: nginx:stable
        ports:
        - containerPort: 80
```

Apply and scale:

```bash
kubectl apply -f Kubernetes/deployments/nginx-deployment.yaml
kubectl scale deployment/nginx-deployment --replicas=4
kubectl get pods -l app=nginx-deploy
```

### Expose a Deployment with a Service

Service manifest (Kubernetes/services/nginx-service.yaml):

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx-deploy
  ports:
  - port: 80
    targetPort: 80
  type: ClusterIP
```

Apply:

```bash
kubectl apply -f Kubernetes/services/nginx-service.yaml
kubectl get svc nginx-service
```

For NodePort (quick switch):

```bash
kubectl patch svc/nginx-service -p '{"spec":{"type":"NodePort"}}'
kubectl get svc nginx-service
```

---

## Debugging & common commands

Get pod status and detailed events:

```bash
kubectl get pods
kubectl describe pod <pod-name>
kubectl get events --sort-by=.metadata.creationTimestamp
```

View logs:

```bash
kubectl logs pod/<pod-name>                 # single container pod
kubectl logs pod/<pod-name> -c <container>  # multi-container pod
kubectl logs deployment/<deployment-name>   # logs from one pod (use -l selector and -c)
```

Execute a shell inside a pod:

```bash
kubectl exec -it pod/<pod-name> -- /bin/sh
kubectl exec -it pod/<pod-name> -c <container> -- /bin/bash
```

Delete and cleanup:

```bash
kubectl delete -f Kubernetes/pods/nginx-pod.yaml
kubectl delete deployment nginx-deployment
kubectl delete svc nginx-service
```

---

## Best practices & notes

- Keep Pods immutable: use Deployments or other controllers for managed workloads.
- Don’t run production workloads as a single Pod without controllers (Deployments/StatefulSets).
- Always request and limit resources (cpu/memory) in production manifests.
- Prefer readiness and liveness probes for reliable rollouts.
- Use labels and selectors consistently for discoverability.

---

## Testing locally

- For lightweight local clusters, use kind or k3d:
  - kind: https://kind.sigs.k8s.io/
  - k3d: https://k3d.io/
- Use `kubectl apply -f` to apply manifests and `kubectl port-forward` to test services locally.

---

## Contributing

Contributions are welcome. Suggested workflow:
1. Fork the repo.
2. Create a feature branch: git checkout -b feature/my-example
3. Add a clear YAML manifest or script in the appropriate folder under Kubernetes/.
4. Update README.md if adding a new folder or pattern.
5. Open a pull request describing what you added and why.

Please follow Kubernetes YAML style and include short explanatory comments in manifests where helpful.

---

## License

This repository is provided under the MIT License. See LICENSE for details.

---

## Contact

Maintainer: Sheersh123
- GitHub: https://github.com/Sheersh123

---
