# Understanding ConfigMap

A ConfigMap is a Kubernetes resource used to store configuration data in key-value pairs. It allows you to decouple configuration artifacts from image content to keep containerized applications portable. ConfigMaps are particularly useful for storing environment variables, configuration files, and other configuration data that can be consumed by your applications running in Pods.

### Why Do We Need ConfigMap?

Consider a scenario where you are trying to run an image you created. You need to set several environment variables. Doing this directly within Pods would involve maintaining a lot of `name` and `value` arrays, which can become very difficult to manage as the number of variables grows.

A better way to manage these environment variables is to use ConfigMaps. ConfigMaps do more than just store environment variables; they also store configuration files and other types of configuration data. Think of a ConfigMap as a centralized place to manage your configuration, making it easier and more organized.

In traditional programming, you might use a `.env` file to store environment variables. This approach helps in managing and separating configuration data from the application code. Similarly, in Kubernetes, ConfigMaps serve this purpose but in a more structured and Kubernetes-native way.

## How to Create a ConfigMap

You can create a ConfigMap using a YAML file or directly from literal values or files using `kubectl`.

### Creating a ConfigMap from a YAML File

Save the following content to `configmap.yaml`:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-config
data:
  APP_ENV: "development"
  APP_DEBUG: "true"
  DATABASE_URL: "mysql://user:password@host:3306/dbname"
```

or you can create using a the command:

```bash
kubectl create configmap any-name --dry-run=client -o yaml > configmap.yaml
```

### Apply the YAML file:

```bash
kubectl create -f configmap.yaml
```

## How to Inject ConfigMap into a Pod

There are several ways to inject ConfigMap data into a Pod.

### Using envFrom to Load All Variables

```yaml
apiVersion: v1
kind: Pod
metadata:
    name: my-pod
spec:
    containers:
    - name: my-container
        image: nginx
        envFrom:
        - configMapRef:
            name: my-config
```

### Using valueFrom to Load Specific Variables

```yaml
apiVersion: v1
kind: Pod
metadata:
    name: my-pod
spec:
    containers:
    - name: my-container
        image: nginx
        env:
        - name: APP_ENV
            valueFrom:
            configMapKeyRef:
                name: my-config
                key: APP_ENV
        - name: DATABASE_URL
            valueFrom:
            configMapKeyRef:
                name: my-config
                key: DATABASE_URL
```

### Mounting ConfigMap as Volumes

```yaml
  Copy code
  apiVersion: v1
  kind: Pod
  metadata:
    name: my-pod
  spec:
    containers:
        - name: my-container
          image: nginx
          volumeMounts:
            - name: config-volume
            mountPath: /etc/config
            readOnly: true
    volumes:
    - name: config-volume
      configMap:
        name: my-config
```

## How to Get and Describe ConfigMaps Using kubectl

Get ConfigMaps

```bash
kubectl get configmaps
```

Describe a ConfigMap

```bash
kubectl describe configmap my-config
```
