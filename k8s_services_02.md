# Kubernetes Services — Day 17

## Why is a Service needed?

Pods are temporary. Their IP addresses can change when Pods are recreated.

A **Service** provides a stable way to reach a group of Pods.

```text
                 Service
                    |
          ┌─────────┼─────────┐
          ↓         ↓         ↓
        Pod-1     Pod-2     Pod-3
```

The Service uses **labels/selectors** to find the correct Pods.

> **Deployment manages Pods. Service provides stable access to Pods.**

---

## Service Ports

For a NodePort Service, you may see three ports:

```yaml
ports:
  - port: 80
    targetPort: 80
    nodePort: 30080
```

| Port | Meaning |
|---|---|
| `nodePort` | Port exposed on the Kubernetes Node for external access |
| `port` | Port exposed by the Service inside the cluster |
| `targetPort` | Port on the selected Pod where the application is listening |

Example:

```text
Client
  |
  | Node-IP:30080
  ↓
Node :30080
  |
  ↓
Service :80
  |
  ↓
Pod :80
  |
  ↓
Application
```

**Important:** `targetPort` does not "open" a new port on the Pod. It tells the Service which application port on the selected Pod should receive the traffic.

---

# Service Types

## 1. ClusterIP

Default Service type.

Used for **internal communication inside the cluster**.

```text
Application A
     |
     ↓
ClusterIP Service
     |
     ↓
Backend Pods
```

Use it when the application does not need to be directly accessible from the internet.

**Memory:** `ClusterIP = internal access`

---

## 2. NodePort

Exposes the Service through a port on the Kubernetes Node.

Example:

```text
Node IP: 192.168.1.10
NodePort: 30080
```

External access:

```text
http://192.168.1.10:30080
```

Flow:

```text
Internet
   |
   | Node-IP:30080
   ↓
Node
   |
   ↓
Service
   |
   ↓
Pods
```

NodePort normally uses a port from the Kubernetes NodePort range (`30000–32767` by default).

**Memory:** `NodePort = Node IP + NodePort`

---

## 3. LoadBalancer

Used mainly in cloud environments to provide an **external load balancer**.

```text
Internet
    |
    ↓
Cloud Load Balancer
    |
    ↓
Kubernetes Service
    |
    ↓
Pods
```

The user gets one external entry point instead of directly using Node IPs and a NodePort.

**Memory:** `LoadBalancer = managed external entry point`

---

# NodePort vs LoadBalancer

NodePort **can** expose an application to the internet. LoadBalancer exists because it provides a better, managed external entry point, especially for cloud and multi-node environments.

### NodePort

```text
Internet
   |
   ↓
Node-IP:30080
   |
   ↓
Service
   |
   ↓
Pods
```

### LoadBalancer

```text
Internet
   |
   ↓
Cloud Load Balancer
   |
   ↓
Service
   |
   ↓
Pods
```

With multiple nodes:

```text
             Cloud Load Balancer
                      |
          ┌───────────┼───────────┐
          ↓           ↓           ↓
        Node 1      Node 2      Node 3
          |           |           |
          └───────────┼───────────┘
                      ↓
                   Service
                      |
                    Pods
```

### Simple distinction

| Type | Main purpose |
|---|---|
| **ClusterIP** | Internal access |
| **NodePort** | External access through Node IP + port |
| **LoadBalancer** | External access through a managed/cloud load balancer |

---

# What to remember for interviews

1. A **Service gives stable access to Pods**.
2. A Service uses **selectors to identify Pods**.
3. **ClusterIP** is for internal cluster communication.
4. **NodePort** exposes a Service through a Node's port.
5. **LoadBalancer** provides an external load balancer, commonly in cloud environments.
6. `nodePort` = Node-facing port, `port` = Service port, `targetPort` = Pod/application port.
7. **Deployment manages Pods; Service exposes/connects to Pods.**

---

# Scenario-Based Questions

### 1. Internal backend

You have a frontend Pod and backend Pods. The backend should only be accessible from inside the Kubernetes cluster.

**Which Service type would you choose and why?**

---

### 2. Simple external testing

You have one Kubernetes Node and want to quickly access your application from your laptop using the Node's IP.

**Which Service type would you choose? What URL would you use?**

---

### 3. Cloud production application

Your application runs on multiple Kubernetes Nodes in AWS and needs a proper external entry point.

**Which Service type would you choose and why?**

---

### 4. Port understanding

Your application listens on port `5000` inside the Pod. You want external access using NodePort `30080`, while the Service exposes port `80`.

What should these values be?

```yaml
port: ?
targetPort: ?
nodePort: ?
```

---

## Stop Here

For a fresher, you do **not** need to go deeper into kube-proxy, iptables/IPVS, EndpointSlices, or CNI networking at this stage.
