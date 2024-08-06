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

## Taints and Tolerations

Taints and tolerations work together to ensure that Pods are not scheduled onto inappropriate nodes. They allow nodes to repel a set of Pods unless those Pods explicitly tolerate the taints applied to those nodes.

- **Taint**: Used to repel a set of Pods from a Node. By default, Pods do not tolerate any taints, meaning if a Node has a taint, no Pod will be scheduled on that Node unless it has a matching toleration.
- **Toleration**: Used to allow a Pod to be scheduled on a Node with specific taints.

Taints are applied to Nodes, while tolerations are applied to Pods.

### Setting a Taint on a Node

#### Command Schema

```bash
kubectl taint nodes <node_name> <key>=<value>:<taint-effect>
```

#### Taint Effects

- `NoSchedule`: Pods that do not tolerate the taint will not be scheduled on the Node.
- `PreferNoSchedule`: Kubernetes will try to avoid placing Pods that do not tolerate the taint on the Node.
- `NoExecute`: Existing Pods that do not tolerate the taint will be evicted, and new Pods that do not tolerate the taint will not be scheduled on the Node.

#### Example

```bash
kubectl taint nodes node1 key1=value1:NoSchedule
```

### Set a Toleration on a Pod

```bash
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    env: test
spec:
  containers:
  - name: nginx
    image: nginx
    imagePullPolicy: IfNotPresent
  tolerations:
  - key: "key1"
    operator: "Exists"
    effect: "NoSchedule"

```

#### Alternative YAML Schema

```bash
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    env: test
spec:
  containers:
  - name: nginx
    image: nginx
    imagePullPolicy: IfNotPresent
  tolerations:
  - key: "key1"
    value: "value1"
    operator: "Equal"
    effect: "NoSchedule"
```

### Removing Taints

To remove a taint from a Node, use the following command:

```bash
kubectl taint nodes node1 key1=value1:NoSchedule-
```

## Node Affinty

Node affinity is the opposite of taints. It is used to attract Pods to specific Nodes based on labels. This can help ensure that Pods are scheduled on Nodes with certain characteristics, such as geographic location or hardware capabilities.

### Types of Node Affinity

- `requiredDuringSchedulingIgnoredDuringExecution`: Specifies that the rule must be met for the Pod to be scheduled on the Node.
- `preferredDuringSchedulingIgnoredDuringExecution`: Specifies that the rule is preferred but not mandatory for scheduling the Pod on the Node.

#### Example YAML for Node Affinity

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
    - name: nginx
      image: nginx
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
          - matchExpressions:
              - key: disktype
                operator: In
                values:
                  - ssd
      preferredDuringSchedulingIgnoredDuringExecution:
        - weight: 1
          preference:
            matchExpressions:
              - key: zone
                operator: In
                values:
                  - east
                  - west
```

## Additional Information

### _NodeSelector_

NodeSelector is a simpler form of node affinity that does not distinguish between required and preferred scheduling. It is specified directly in the Pod spec and uses exact matches for labels.

### Example YAML for NodeSelector

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
    - name: nginx
      image: nginx
  nodeSelector:
    disktype: ssd
```
