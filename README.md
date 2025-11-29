# Kubernetes-Pods

A curated collection of Kubernetes manifests, examples, and learning experiments focused on Pods and basic workload primitives. This repository is intended for developers and learners who want hands-on examples to learn how Pods, Deployments, Services, and basic networking behave in Kubernetes.

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
- [Contributing](#contributing)
- [License](#license)
- [Contact](#contact)

---

## Overview

This repo is intentionally small and practical — each manifest demonstrates one concept with minimal extra YAML. Use it to practice kubectl commands, to teach others, or to reference for small demos.

---

## Repository layout

Typical directory structure:

- manifests/
  - pods/            # single pod examples
  - deployments/     # Deployment and ReplicaSet examples
  - services/        # Service examples (ClusterIP, NodePort, LoadBalancer)
  - init-containers/ # examples using init containers
  - volumes/         # examples using emptyDir, hostPath, pvc
- examples/          # higher-level example scenarios
- scripts/           # helper scripts (local cluster creation, cleanup)
- README.md

(If your repository differs, adapt the layout section to match your current tree.)

---

## Prerequisites

- kubectl (v1.20+ recommended)
- A Kubernetes cluster: Minikube, kind, k3d, microk8s, or an existing cloud cluster
- (Optional) docker or podman to build images locally

---

## Quick start examples

All commands assume your kubectl context is set to the cluster where you want to run these examples.

### Create a simple Pod

Save this as manifests/pods/nginx-pod.yaml:

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
kubectl apply -f manifests/pods/nginx-pod.yaml
kubectl get pods -l app=nginx-demo
```

Port-forward to test in your browser:

```bash
kubectl port-forward pod/nginx-demo 8080:80
# open http://localhost:8080
```

### Create a Deployment and scale

Deployment manifest (manifests/deployments/nginx-deployment.yaml):

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
kubectl apply -f manifests/deployments/nginx-deployment.yaml
kubectl scale deployment/nginx-deployment --replicas=4
kubectl get pods -l app=nginx-deploy
```

### Expose a Deployment with a Service

Service manifest (manifests/services/nginx-service.yaml):

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
kubectl apply -f manifests/services/nginx-service.yaml
kubectl get svc nginx-service
```

For NodePort:

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
kubectl delete -f manifests/pods/nginx-pod.yaml
kubectl delete deployment nginx-deployment
kubectl delete svc nginx-service
```

---

## Best practices & notes

- Keep Pods immutable: use Deployments for managed workloads.
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
3. Add a clear YAML manifest or script in the appropriate folder.
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

If you'd like, I can:
- commit this README.md to a branch and open a pull request,
- or push directly to main (if you prefer and provide permission).
