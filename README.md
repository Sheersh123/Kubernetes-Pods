# Kubernetes-Pods

![Kubernetes Pods banner](assets/banner.svg)

A curated, hands‑on collection of Kubernetes Pod manifests, patterns, and best practices. This repository is focused on Pod-level concepts (single- and multi-container Pods, init containers, lifecycle hooks, volumes, probes, resources, scheduling, and debugging) to help developers, SREs, and educators quickly learn, test, and adopt production‑ready Pod configurations.

Table of contents
- About
- Who this is for
- Repository layout
- Quick start (apply an example)
- Detailed examples (what's included & why)
- Common workflows and commands
- Best practices & guidance
- Converting Pods to higher-level controllers
- Testing & local development (minikube / kind / k3s)
- Contributing guide
- Troubleshooting & FAQ
- License & contact
- Useful references

About
-----
Kubernetes Pods are the smallest deployable units in Kubernetes. This repo provides small, well-documented manifests that demonstrate Pod patterns and practical configurations you can use directly or adapt for real workloads.

Who this is for
---------------
- Developers learning how Pods work (lifecycle, startup order, volumes).
- SREs and platform engineers experimenting with Pod-level patterns before recommending controllers.
- Instructors preparing concise, focused examples for talks or labs.
- Anyone wanting Pod-focused examples without the complexity of Deployments/StatefulSets.

Repository layout
----------------
- assets/
  - banner.svg                         # Repository banner and other assets
- manifests/
  - simple/
    - simple-pod.yaml                  # Minimal single container Pod
  - multi-container/
    - sidecar-pod.yaml                 # App + sidecar pattern
  - init-containers/
    - init-example.yaml                # Init container examples
  - volumes/
    - emptydir-pod.yaml
    - hostpath-pod.yaml
    - pvc-pod.yaml
  - probes/
    - probes-http-pod.yaml
    - probes-exec-pod.yaml
  - resources/
    - resource-requests-limits.yaml
  - scheduling/
    - node-selector-pod.yaml
    - affinity-pod.yaml
  - networking/
    - pod-service.yaml
- scripts/                              # Optional helper scripts (build, load image)
- README.md
- LICENSE

Quick start
-----------
1. Clone the repository:
   ```bash
   git clone https://github.com/Sheersh123/Kubernetes-Pods.git
   cd Kubernetes-Pods
   ```

2. Start a local cluster (examples)
   - minikube:
     ```bash
     minikube start
     ```
   - kind:
     ```bash
     kind create cluster --name kp-cluster
     ```
   - k3s: follow your preferred installer

3. Apply a simple Pod manifest:
   ```bash
   kubectl apply -f manifests/simple/simple-pod.yaml
   kubectl get pods --all-namespaces
   ```

4. Inspect:
   ```bash
   kubectl describe pod simple-pod
   kubectl logs simple-pod
   kubectl exec -it simple-pod -- /bin/sh
   ```

Detailed examples included
--------------------------
Each folder under manifests/ contains small, focused examples. Every manifest includes comments explaining each field and why it's used.

1) Simple Pod (manifests/simple/simple-pod.yaml)
   - Minimal Pod manifest demonstrating metadata, labels, and a single container.
   - Good starting point for kubectl apply and inspect.

2) Multi-container Pod / Sidecar (manifests/multi-container/sidecar-pod.yaml)
   - Demonstrates the sidecar pattern: primary app container + helper container (e.g., logger, proxy).
   - Shows shared volumes and why containers share the same network / PID namespace (if enabled).

3) Init Containers (manifests/init-containers/init-example.yaml)
   - Demonstrates init containers that run to completion before main containers start.
   - Use cases: waiting for external dependency, seeding configuration, altering filesystem permissions.

4) Volumes
   - emptyDir (ephemeral storage for a Pod lifecycle)
   - hostPath (bind host storage — *use with caution*)
   - PVC + StorageClass (persistent storage example and mounting)
   Files: manifests/volumes/{emptydir-pod.yaml, hostpath-pod.yaml, pvc-pod.yaml}

5) Probes (Liveness & Readiness)
   - Example of HTTP, TCP, and Exec probes.
   - Documents initialDelaySeconds, periodSeconds, failureThreshold to avoid false positives.
   Files: manifests/probes/{probes-http-pod.yaml, probes-exec-pod.yaml}

6) Resource Requests & Limits
   - Recommended patterns for requests and limits to help scheduler and QoS classification.
   File: manifests/resources/resource-requests-limits.yaml

7) Scheduling (nodeSelector, nodeAffinity, podAffinity/antiAffinity)
   - Examples for pinning Pods to specific nodes or topology domains.
   Files: manifests/scheduling/{node-selector-pod.yaml, affinity-pod.yaml}

8) Networking & Exposure
   - How to expose a Pod using a Service (ClusterIP/NodePort/LoadBalancer) and port-forward examples.
   File: manifests/networking/pod-service.yaml

Common kubectl commands & workflows
----------------------------------
- Apply a manifest:
  ```bash
  kubectl apply -f manifests/<path-to-file>.yaml
  ```
- Delete:
  ```bash
  kubectl delete -f manifests/<path-to-file>.yaml
  ```
- Get pods:
  ```bash
  kubectl get pods -o wide
  ```
- Describe (events, scheduling details):
  ```bash
  kubectl describe pod <pod-name>
  ```
- Logs:
  ```bash
  kubectl logs <pod-name>
  kubectl logs <pod-name> -c <container-name>
  ```
- Exec:
  ```bash
  kubectl exec -it <pod-name> -c <container-name> -- /bin/sh
  ```
- Port forwarding:
  ```bash
  kubectl port-forward pod/<pod-name> 8080:80
  ```
- Watch resource changes:
  ```bash
  kubectl get pods -w
  ```

Best practices
--------------
- Keep containers in the same Pod tightly coupled (shared lifecycle, storage).
- Prefer higher-level controllers (Deployment, StatefulSet) in production for replicas and rolling updates.
- Always set resource requests and limits for predictable scheduling.
- Use readiness probes to avoid sending traffic to non-ready Pods.
- Use liveness probes to recover unhealthy containers automatically.
- Avoid hostPath unless absolutely necessary (security risks).
- Use explicit image tags (avoid :latest) and immutable images for production.
- Use RBAC to limit what Pods (via ServiceAccounts) can do.
- Plan storage carefully: emptyDir is ephemeral; use PVCs for persistence.

Converting Pod manifests to controllers
--------------------------------------
Pods are ephemeral and don't provide replicas or rolling updates. To run Pods in production, convert to a Deployment or StatefulSet:

Example: simple Pod -> Deployment
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: simple-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: simple
  template:
    metadata:
      labels:
        app: simple
    spec:
      containers:
      - name: nginx
        image: nginx:1.25-alpine
        ports:
        - containerPort: 80
```

Testing & local development tips
-------------------------------
- kind: to test local images created with Docker on the host:
  ```bash
  docker build -t myimage:dev .
  kind load docker-image myimage:dev --name kp-cluster
  ```
- minikube: to access services using minikube service:
  ```bash
  minikube service <service-name>
  ```
- Use imagePullPolicy: If you're iterating with local images, set imagePullPolicy: IfNotPresent or Never accordingly.
- Use small reproducible images (busybox, curl) in examples to speed up tests.
- Use scripts/ to automate building and loading images; add a README in scripts/ documenting usage.

Contributing
------------
We welcome contributions of new examples, improvements to existing manifests, and clearer documentation.

How to contribute:
1. Fork the repository.
2. Create a feature branch: git checkout -b feat/<short-description>
3. Add/modify manifests or docs in the appropriate folder under manifests/.
4. Add a short README or comments if your example requires context.
5. Run basic sanity checks on a cluster locally (minikube/kind).
6. Create a Pull Request with a clear description of what you changed and why.

Contributor guidelines:
- Keep examples small and focused (one concept per manifest).
- Add comments in YAML when something might be surprising.
- Use descriptive filenames: manifests/<category>/<what-it-demonstrates>.yaml
- If adding images you build locally, include Dockerfile or links to public images.

Troubleshooting & FAQ
---------------------
Q: Pod stays in ImagePullBackOff or ErrImagePull
A:
- Check kubectl describe pod <pod>
- Verify image name and registry credentials (imagePullSecrets)
- Ensure cluster nodes have network access to the registry

Q: Pod is Pending
A:
- kubectl describe pod shows scheduling messages: insufficient CPU/memory or node selectors mismatched.
- Check nodes with kubectl get nodes -o wide and resource usage.

Q: Init container hangs / fails
A:
- View init container logs: kubectl logs <pod> -c <init-container-name>
- Ensure termination conditions for init container succeed before main containers start.

Q: Liveness probe keeps restarting containers
A:
- Check probe path, port, and timings. Increase initialDelaySeconds to give app more startup time.

FAQ: Where are Deployments and other controllers?
- This repo emphasizes Pod-level learning. If you want conversion examples to Deployments/StatefulSets, check the "Converting Pods to controllers" section or open an issue to request examples.

Security considerations
-----------------------
- Avoid running containers as root unless necessary — prefer securityContext to set runAsUser and drop capabilities.
- Limit volumes (hostPath) usage and consider Pod Security Policies / OPA / Gatekeeper for enforcing constraints.
- Review image provenance and scanning results for container images in production.

Repository maintenance & roadmap
-------------------------------
Planned improvements:
- Add common real-world examples (sidecar for log forwarding, init container DB migrations).
- Add small HOWTOs for converting Pod examples to Deployments, Jobs and CronJobs.
- Add GitHub Actions workflow to validate YAML manifests (kubeval/kube-lint).
If you'd like to help with any item, open an issue and tag it with the appropriate label.

License
-------
This repository is available under the MIT License. See LICENSE for full terms.

Acknowledgements & credits
--------------------------
- Inspired by official Kubernetes docs and common community examples.
- Banner created for the repository (assets/banner.svg).

Contact / Maintainer
--------------------
Maintainer: Sheersh123
GitHub: https://github.com/Sheersh123

If you need help converting Pod examples into Deployments or want custom examples for a use case (databases, sidecars, init workflows), open an issue describing your scenario.

Useful references
-----------------
- Official Kubernetes docs — Pods: https://kubernetes.io/docs/concepts/workloads/pods/
- Probes tutorial: https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/
- Volumes: https://kubernetes.io/docs/concepts/storage/volumes/
- Best practices: https://kubernetes.io/docs/setup/best-practices/

Changelog
---------
See commit history for changes. If you add a substantial example or change semantics, include a short note in the top of the modified manifest or create a CHANGELOG.md entry.

Enjoy experimenting and learning with Pods! If you want, I can:
- Add GitHub Actions that validate YAML and run kubeval.
- Convert a few core Pod examples into Deployments/StatefulSets and add side-by-side comparisons.
- Generate a PDF/markdown cheat sheet of common kubectl commands from the docs above.