# Kubernetes — ConfigMap, Secret & Volumes

## 1. ConfigMap

A **ConfigMap stores non-sensitive configuration separately from application code.**

Examples:
- Application mode
- Port
- URLs
- Feature flags

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  APP_MODE: production
```

Connect it to a Pod using `configMapKeyRef`:

```yaml
env:
  - name: APP_MODE
    valueFrom:
      configMapKeyRef:
        name: app-config
        key: APP_MODE
```

```text
Pod
 |
 | configMapKeyRef
 ↓
ConfigMap
 |
 ↓
APP_MODE=production
```

---

## 2. Secret

A **Secret stores sensitive configuration** such as passwords, API keys and tokens.

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: db-secret
stringData:
  DB_PASSWORD: mypassword
```

Connect it to a Pod using `secretKeyRef`:

```yaml
env:
  - name: DB_PASSWORD
    valueFrom:
      secretKeyRef:
        name: db-secret
        key: DB_PASSWORD
```

```text
Pod
 |
 | secretKeyRef
 ↓
Secret
 |
 ↓
DB_PASSWORD
```

### Remember

```text
ConfigMap → non-sensitive configuration
Secret    → sensitive configuration
```

ConfigMaps and Secrets can also be mounted as files, but for now focus on using them as environment variables.

---

# 3. Volumes

Container-local data should not be relied on to survive when a Pod/container is replaced.

A **Volume provides storage that can be mounted into a Pod.**

```text
Pod
 |
 ↓
Container
 |
 ↓
Volume
 |
 ↓
Storage
```

Not every Pod needs persistent storage.

### No persistent data needed

```text
NGINX Pod
   ↓
Serves website
```

A persistent volume may not be necessary.

### Data must survive Pod replacement

For a database:

```text
Database Pod
     |
     ↓
    PVC
     |
     ↓
    PV
     |
     ↓
Persistent Storage
```

If the Pod is replaced, the persistent data remains.

---

# 4. PV vs PVC

### PersistentVolume (PV)

> Storage resource available to Kubernetes.

### PersistentVolumeClaim (PVC)

> Application's request for persistent storage.

Simple memory:

```text
PV  = storage supply
PVC = storage request
```

Flow:

```text
Application
    |
    ↓
   PVC       "I need storage"
    |
    ↓
   PV        "Here is storage"
    |
    ↓
Persistent Storage
```

---

# 5. With a Deployment

You normally don't configure every Pod separately.

Put the ConfigMap, Secret or volume configuration inside the Deployment's **Pod template**.

```text
Deployment
    |
    ↓
Pod Template
    |
    ├── ConfigMap
    ├── Secret
    └── Volume
    |
    ↓
ReplicaSet
    |
    ├── Pod
    ├── Pod
    └── Pod
```

With `replicas: 3`, all three Pods receive the configuration defined in the template.

---

# Quick Interview Summary

| Concept | Purpose |
|---|---|
| **ConfigMap** | Non-sensitive configuration |
| **Secret** | Sensitive configuration |
| **Volume** | Storage mounted into a Pod |
| **PV** | Persistent storage available to Kubernetes |
| **PVC** | Application's request for persistent storage |

### Must remember

- `configMapKeyRef` in deployment.spec.env: → connects a ConfigMap value to a Pod.
- `secretKeyRef` → connects a Secret value to a Pod.
- Not every Pod needs persistent storage.
- Use persistent storage when data must survive Pod replacement.
- With Deployments, configure these in the **Pod template**.


**Mental model:**

```text
ConfigMap → configuration
Secret    → sensitive configuration
Volume    → mounted storage
PV        → storage supply
PVC       → storage request
```
