# Kubernetes Configuration

## Security Context

A security context defines privilege and access control settings for a Pod or Container. Security contexts are used to set the user ID (UID) and and to control access privileges and capabilities. You can set these settings at the Pod level or the Container level, with Container-level settings overriding those at the Pod level.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: multi-pod
spec:
  securityContext:
    runAsUser: 1001
  containers:
    - image: ubuntu
      name: web
      command: ["sleep", "5000"]
      securityContext:
        runAsUser: 1002
    - image: ubuntu
      name: sidecar
      command: ["sleep", "5000"]
```

## Service Accounts

Kubernetes supports two types of accounts: User Accounts and Service Accounts. Service Accounts are primarily used by applications and processes (e.g., monitoring systems, third-party applications) running in the cluster.

### Creating a Service Account

```bash
kubectl create serviceaccount account_name
```

### Listing Service Accounts

```bash
kubectl get servcieaccounts
```

### Use a service account in a pod

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  serviceAccountName: my-service-account
  containers:
    - name: my-container
      image: nginx
```

## Resource Requirements

### Resource requests

Resource requests specify the minimum amount of CPU and memory that a container needs. Kubernetes uses this information to schedule the container on a node that can meet these resource requests.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
    - name: my-container
      image: nginx
      resources:
        requests:
          memory: "64Mi"
          cpu: "250m"
```

### Resource limits

Resource limits specify the maximum amount of CPU and memory that a container can use. This helps to control the resource usage of containers to prevent them from consuming too many resources on a node.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
    - name: my-container
      image: nginx
      resources:
        limits:
          memory: "128Mi"
          cpu: "500m"
```

### Understanding resource Behavior

We several scenarios when creates pods in a node.

There are several scenarios when creating Pods on a node:

- No request and no limit set: The container can use as much CPU and memory as is available on the node, potentially affecting other containers.
- No request and limit set: The container can burst up to the limit when resources are available but has no guaranteed minimum.
- Request and limit set: The container is guaranteed the requested resources and can use up to the limit.
- Request and no limit set: The container is guaranteed the requested resources and can use any amount of resources available on the node.

### LimitRange

A LimitRange sets minimum and maximum constraints on the resources that Pods or Containers can use in a namespace. This helps in enforcing resource usage policies.

```yaml
apiVersion: v1
kind: LimitRange
metadata:
  name: limit-range-example
  namespace: my-namespace
spec:
  limits:
    - default:
        cpu: "500m"
        memory: "128Mi"
      defaultRequest:
        cpu: "250m"
        memory: "64Mi"
      type: Container
```

### Resource Quota

A ResourceQuota sets aggregate limits on the amount of resources that a namespace can use. This helps in controlling the overall resource consumption in a namespace.

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: resource-quota-example
  namespace: my-namespace
spec:
  hard:
    requests.cpu: "1"
    requests.memory: "1Gi"
    limits.cpu: "2"
    limits.memory: "2Gi"
```
