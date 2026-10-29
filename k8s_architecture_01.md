# Kubernetes Notes

## Deployment

A Deployment manages your Pods.

Instead of saying:

> "Run this Pod."

you usually say:

> "I want 3 copies of my application running."

```text
Deployment
     │
     ▼
ReplicaSet
     │
 ┌───┼───┐
 ▼   ▼   ▼
Pod Pod Pod
```

## ReplicaSet

A ReplicaSet makes sure the desired number of Pods exist.

You generally create a Deployment, not a ReplicaSet directly.

You specify the number of replicas in the Deployment. Deployment tells the ReplicaSet that 3 Pods should be running.

## Service

Pods are temporary. They can be created, deleted, and recreated. Their IP can change.

So how does another application reliably communicate with it? **Service.**

The Service provides a stable way to reach a group of Pods.

## ConfigMap

Applications need configuration.

For example:

```text
APP_ENV=production
PORT=5000
DB_HOST=mydatabase
```

Instead of putting this directly inside your application image, Kubernetes can store non-sensitive configuration in a ConfigMap.

**ConfigMap --> Pod**

ConfigMap = non-sensitive configuration.

## Secret

Secrets are for sensitive information.

Examples:

```text
DB_PASSWORD
API_KEY
TOKEN
```

**Secret --> Pod**

Secret = sensitive configuration/data.

One important correction to keep in mind:

Kubernetes Secrets are not automatically a secure vault.

## Namespace

A Namespace logically separates resources inside a cluster. (dev/testing/prod may have different services, configmaps, secret, deployments)

## Ingress

Ingress handles HTTP/HTTPS traffic coming into applications.

```text
example.com/api
      │
      ▼
   Service A

example.com/shop
      │
      ▼
   Service B
```

## Volume

Containers have temporary filesystem storage. If the container disappears, data stored only inside that container disappear with it.

A Volume provides storage that can live beyond the lifecycle of an individual container. i.e. Volume = storage used by Pods/containers.

```text
Pod
│
├── Container
│
└── Volume
       │
       ▼
      Data
```

## Kubernetes Resource Flow

```text
Deployment → ReplicaSet → Pods → Service
```

Deployment manages the desired number of Pods through a ReplicaSet, the Scheduler places Pods on Worker Nodes, kubelet makes sure pods are running and the container runtime run container in those pods, and a Service provides stable network communication to the Pods.

## What happens when you create a Deployment?

Kubernetes creates a ReplicaSet for the Deployment, and the ReplicaSet creates the required number of Pods.

## Useful Commands

### View container logs

```bash
kubectl logs <pod-name>
```

Example:

```bash
kubectl logs nginx-deployment-xxxxx-yyyyy
```

This shows the container's logs.

### Enter a running container

```bash
kubectl exec -it <pod-name> -- /bin/bash
```

```bash
exit
```

### Scale the Deployment

```bash
kubectl scale deployment <deployment name> --replicas=5
```

### Delete a Pod — important experiment

```bash
kubectl delete pod <pod-name>
```

Old Pod → Terminating  
New Pod → Creating

Kubernetes create a new Pod Because you told Kubernetes:

Desired 2 pods != actual 1 pod

ReplicaSet notices:

1 != 2

and creates another Pod.

### Delete the Deployment

```bash
kubectl delete deployment <deployment-name>
```

The Pods should disappear because they were managed by that Deployment.

## Command Reference

| Command | Purpose |
| --- | --- |
| `kubectl get pods` | List Pods |
| `kubectl get deployments` | List Deployments |
| `kubectl get replicasets` | List ReplicaSets |
| `kubectl get all` | Show common resources |
| `kubectl describe pod <name>` | Detailed Pod information |
| `kubectl logs <name>` | View container logs |
| `kubectl exec -it <name> -- /bin/bash` | Enter container |
| `kubectl apply -f file.yaml` | Create/update resources |
| `kubectl delete -f file.yaml` | Delete resources from YAML |
| `kubectl delete pod <name>` | Delete a Pod |
| `kubectl scale deployment <name> --replicas=5` | Change replica count |
