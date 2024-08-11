# POD Design

## Labels, Selectors, and Annotations

### Labels

Labels are used to:

- Organize and manage resources.
- Select and filter resources using label selectors.
- Facilitate rolling updates, scaling, and other operations.

#### Example

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
  labels:
    environment: production
    team: frontend
spec:
  containers:
    - name: my-container
      image: nginx
```

### Selectors

Selectors are used to specify which objects are relevant for a particular operation based on their labels.

- **Equality-Based Selectors**: These match resources that have a label with a specific key-value pair.
- **Set-Based Selectors**: These match resources that have a label with a key that matches a set of values.

#### Examples

Return the Pods with a matching key and value:

```bash
kubectl get pod --selector key=value
```

Return the Pods with multiple matching keys and values:

```bash
kubectl get pod --selector key1=value1,key2=value2,key3=value3
```

Return multiple resource types with matching keys and values:

```bash
kubectl get pods,deployments,services,replicaSets --selector key1=value1,key2=value2,key3=value3
```

Selectors can be used to match Pods in ReplicaSets and Services.

#### Example Usage in YAML

Service Example:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  selector:
    app: my-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 9376
```

ReplicaSet Example:

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: my-replicaset
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
        - name: my-container
          image: my-app-image
```

### Annotations

Annotations are used to add arbitrary metadata to Kubernetes objects. Unlike labels, annotations are not used to identify and select objects but to attach non-identifying information.

#### Example

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
  annotations:
    description: "This is my pod"
    owner: "team-frontend"
spec:
  containers:
    - name: my-container
      image: nginx
```

## Deployment Strategies

### Recreate

In the Recreate strategy, all existing Pods are terminated before new Pods are created. This results in a short period of downtime while the new Pods are being deployed. This strategy is simple but may not be suitable for production environments where high availability is critical.

### Rolling

The Rolling strategy updates Pods incrementally, replacing a few old Pods with new Pods at a time. This ensures that some instances of the application are always available, reducing or eliminating downtime. It's a common choice for updating applications without affecting the overall availability.

### Blue/Green Deployment

In Blue/Green deployment, both the old (Blue) and new (Green) environments are spun up, but user requests are directed to the old environment while the new environment is thoroughly tested. After successful testing, traffic is switched from Blue to Green. This approach requires maintaining two environments, but it allows for a quick rollback if issues arise.

### Canary Deployment

In Canary deployment, the old version continues to run as usual, while a small percentage of user traffic is routed to the new version (Canary). This allows for testing in a live environment with minimal impact. If the Canary deployment is successful, the traffic is gradually increased to the new version until it fully replaces the old version. If issues are detected, the deployment can be rolled back using either the Recreate or Rolling strategy.

## Jobs and CronJobs

### Jobs

A Job in Kubernetes is a type of workload that runs until a specified task is completed. You can control how many times the Job runs by setting the `completions` field, and you can configure the number of Pods to run in parallel using the `parallelism` field. Jobs are ideal for batch processing tasks, such as data processing or report generation.

### CronJobs

CronJobs are a type of Job that runs on a scheduled basis, similar to cron jobs in Linux. You define a schedule in Cron format, and Kubernetes will automatically create Jobs based on that schedule. CronJobs are useful for tasks that need to run periodically, such as database backups or regular data imports.
