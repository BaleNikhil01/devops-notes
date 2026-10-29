# Kubernetes Workloads and Core Resources

## 1. Deployment

A **Deployment** manages an application's Pods and helps keep the
desired number of replicas running. It also supports rolling updates and
rollbacks.

Instead of asking Kubernetes to run one specific Pod, you usually
declare the desired state, such as:

> I want 3 replicas of my application running.

``` text
Deployment
    |
    v
ReplicaSet
    |
    +--------+--------+
    v        v        v
   Pod      Pod      Pod
```

You specify the replica count in the Deployment. Kubernetes creates or
updates a ReplicaSet, which works to maintain that number of Pods.

## 2. ReplicaSet

A **ReplicaSet** ensures that the desired number of matching Pod
replicas exist.

You generally create a Deployment rather than creating a ReplicaSet
directly, because a Deployment provides update and rollback
functionality and manages its ReplicaSets.

For example, if the desired number of replicas is 3 but only 2 Pods are
running, the ReplicaSet attempts to create another Pod.

## 3. Service

Pods are replaceable. They can be deleted and recreated, and a
replacement Pod may have a different IP address.

A **Service** provides a stable network endpoint for reaching a set of
Pods selected by labels. It helps other applications communicate with
those Pods without needing to track each Pod's changing IP address.

Common Service types include:

-   **ClusterIP** --- exposes the Service inside the cluster; this is
    the default.
-   **NodePort** --- exposes the Service on a port on each node.
-   **LoadBalancer** --- requests an external load balancer from a
    supported environment.

## 4. ConfigMap

Applications often need non-sensitive configuration, for example:

``` text
APP_ENV=production
PORT=5000
DB_HOST=mydatabase
```

A **ConfigMap** stores non-confidential configuration separately from
the application image. A Pod can consume ConfigMap values through
environment variables or mounted files.

``` text
ConfigMap
    |
    v
   Pod
```

Do not store passwords, API keys, or other sensitive values in a
ConfigMap.

## 5. Secret

A **Secret** is intended for sensitive configuration, such as:

``` text
DB_PASSWORD
API_KEY
TOKEN
```

A Pod can consume Secret values through environment variables or mounted
files.

``` text
Secret
   |
   v
  Pod
```

**Important:** Kubernetes Secrets are not automatically a secure vault.
By default, Secret data is base64-encoded, which is not encryption. Use
appropriate access controls (RBAC), enable encryption at rest where
supported, and avoid exposing secrets in logs or source control.
External secret-management systems may also be appropriate.

## 6. Namespace

A **Namespace** logically separates and organizes namespaced resources
within a Kubernetes cluster. For example, teams may use separate
namespaces for `dev`, `testing`, and `prod`.

Namespaces can contain resources such as Deployments, Services,
ConfigMaps, and Secrets. They do not, by themselves, provide complete
security or network isolation; additional permissions and policies may
be needed.

Useful commands:

``` bash
kubectl get namespaces
kubectl get pods -n dev
kubectl get deployments -n testing
```

## 7. Ingress

An **Ingress** defines rules for routing HTTP and HTTPS traffic from
outside the cluster to Services, commonly based on hostnames or URL
paths.

Example:

``` text
example.com/api  ---> Ingress ---> Service A
example.com/shop ---> Ingress ---> Service B
```

An Ingress resource requires an **Ingress controller** to implement its
rules. Creating an Ingress object alone does not automatically make the
application reachable from the internet.

## 8. Volumes and Persistent Storage

A container's writable filesystem is generally tied to that container's
lifecycle. If the container is replaced, data stored only in that
filesystem may be lost.

A **Volume** makes storage available to containers in a Pod. The
lifetime and persistence of the data depend on the volume type:

-   Some volumes are temporary and tied to the Pod's lifetime.
-   A **PersistentVolume (PV)** represents storage available to the
    cluster.
-   A **PersistentVolumeClaim (PVC)** is a request for storage by a
    workload.

``` text
Pod
 |
 +-- Container
 |
 +-- Volume mount
        |
        v
      Storage
```

Use a suitable persistent storage option when data must survive Pod
replacement. A volume does not necessarily mean the data will persist
after the Pod is deleted; that depends on the volume type and its
configuration.

## 9. How the Main Components Work Together

``` text
Deployment
    |
    v
ReplicaSet
    |
    v
   Pods <----- Service provides a stable endpoint to reach selected Pods
```

The Deployment declares the desired state and manages ReplicaSets. A
ReplicaSet maintains the desired number of Pods. The scheduler assigns
unscheduled Pods to suitable worker nodes. On each node, the kubelet
works with the container runtime to ensure the containers are running.

A Service provides stable network access to a group of Pods. ConfigMaps
and Secrets supply configuration, Namespaces organize resources, Ingress
can route HTTP/HTTPS traffic to Services, and Volumes provide storage.

## 10. What Happens When You Create a Deployment?

For example, when you apply a Deployment that requests 3 replicas:

1.  The Kubernetes API server validates and stores the requested
    resource.
2.  The Deployment controller creates or updates a ReplicaSet.
3.  The ReplicaSet controller creates Pods to reach the desired replica
    count.
4.  The scheduler assigns each unscheduled Pod to a suitable node.
5.  The kubelet on each selected node works with the container runtime
    to start the containers.
6.  Kubernetes controllers continue reconciling the actual state with
    the desired state.

The exact sequence involves multiple controllers working continuously;
it is not simply one command executing every step in a fixed,
synchronous sequence.

## 11. Useful `kubectl` Commands

### Inspect resources

``` bash
kubectl get pods
kubectl get deployments
kubectl get replicasets
kubectl get services
kubectl get all
kubectl describe pod <pod-name>
```

`kubectl get all` shows several common resource types, but it does
**not** show every resource in the namespace.

### View logs

``` bash
kubectl logs <pod-name>
```

For a Pod with multiple containers, specify the container:

``` bash
kubectl logs <pod-name> -c <container-name>
```

### Run a command inside a container

``` bash
kubectl exec -it <pod-name> -- /bin/sh
```

Use `/bin/bash` instead if Bash is installed in the container:

``` bash
kubectl exec -it <pod-name> -- /bin/bash
```

Not every container image includes a shell. Type `exit` to leave the
shell.

### Apply or delete resources from a YAML file

``` bash
kubectl apply -f file.yaml
kubectl delete -f file.yaml
```

### Scale a Deployment

``` bash
kubectl scale deployment <deployment-name> --replicas=5
```

This changes the desired number of replicas to 5. For a long-term
configuration, also update the Deployment's YAML or other source of
truth so the desired count is not later changed back by your deployment
workflow.

## 12. Important Experiments

### Experiment A: Delete a Pod managed by a Deployment

First, list the Pods:

``` bash
kubectl get pods
```

Delete one Pod:

``` bash
kubectl delete pod <pod-name>
```

If the Pod belongs to a Deployment, Kubernetes should create a
replacement to restore the desired replica count.

For example:

``` text
Desired replicas: 2
Running replicas after deletion: 1
ReplicaSet notices the difference
A replacement Pod is created
```

The replacement Pod may have a different name and IP address.

### Experiment B: Delete a Deployment

``` bash
kubectl delete deployment <deployment-name>
```

The Deployment and the ReplicaSets and Pods it manages are normally
removed as part of the ownership chain. Related resources such as
Services, ConfigMaps, Secrets, and PersistentVolumeClaims are not
automatically deleted just because they are separate resources; delete
them separately if appropriate.

------------------------------------------------------------------------

## Quick Revision Table

  -----------------------------------------------------------------------
  Resource                            Main purpose
  ----------------------------------- -----------------------------------
  Deployment                          Manages application rollout and
                                      desired Pod replicas through
                                      ReplicaSets

  ReplicaSet                          Maintains the desired number of
                                      matching Pods

  Pod                                 Smallest deployable Kubernetes
                                      unit; runs one or more containers

  Service                             Provides a stable network endpoint
                                      for a group of Pods

  ConfigMap                           Stores non-sensitive configuration

  Secret                              Holds sensitive configuration data;
                                      requires appropriate security
                                      controls

  Namespace                           Organizes and separates namespaced
                                      resources within a cluster

  Ingress                             Defines HTTP/HTTPS routing rules;
                                      requires an Ingress controller

  Volume                              Makes storage available to
                                      containers in a Pod

  PersistentVolume (PV)               Represents storage available to the
                                      cluster

  PersistentVolumeClaim (PVC)         Requests storage for a workload
  -----------------------------------------------------------------------

## Interview Answer

**How do Deployment, ReplicaSet, Pods, and Services work together?**

A Deployment declares the desired state of an application and manages
ReplicaSets. A ReplicaSet maintains the required number of Pods. The
scheduler places unscheduled Pods on suitable worker nodes, and the
kubelet works with the container runtime to run their containers. Since
Pods can be replaced and their IP addresses can change, a Service
provides a stable endpoint for reaching the selected Pods.
