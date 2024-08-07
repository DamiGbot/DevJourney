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
