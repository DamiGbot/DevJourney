# Understanding Secrets

A Secret is a Kubernetes resource used to store sensitive data, such as passwords, OAuth tokens, and ssh keys. Secrets are base64 encoded to provide a layer of security, making them more secure than storing sensitive information in plain text in ConfigMaps.

### Why Do We Need Secrets?

Consider a scenario where you have an application that requires a database username and password. Storing these credentials directly in a ConfigMap or Pod specification is not secure because it can be easily accessed by anyone who has access to these resources. Instead, using Secrets provides a more secure way to handle sensitive information.

In traditional programming, you might use environment variables or a `.env` file to store sensitive data. However, these methods are not secure and can be easily compromised. Kubernetes Secrets offer a structured and more secure approach to manage sensitive data in a Kubernetes-native way.

## How to Properly Store Sensitive Data in Secrets

Since storing sensitive data in ConfigMaps is not advised, we use Secrets. To ensure security, we need to encode the sensitive data before storing it in a Secret.

<h4 style="color: red; font-weight: bold;">Warning</h4>

<p style="color: red; font-weight: bold;">Secrets are not encrypted; they are only base64 encoded. This means they can be easily decoded. Do not push your Secret files to a repository. Always ensure they are handled securely.</p>

### Encoding Sensitive Data

To encode data, use the following commands:

```bash
echo -n 'my-username' | base64
echo -n 'my-password' | base64
```

To decode the data, use:

```bash
echo -n 'bXktdXNlcm5hbWU=' | base64 --decode
echo -n 'bXktcGFzc3dvcmQ=' | base64 --decode
```

## How to Create a Secret

### Creating a Secret from a YAML File

Save the following content to `secret.yaml`:

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: my-secret
type: Opaque
data:
  username: bXktdXNlcm5hbWU=
  password: bXktcGFzc3dvcmQ=
```

or you can create using a the command:

```bash
kubectl create secret any-name --from-literal=username=my-username --dry-run=client -o yaml > secret.yaml
```

### Apply the YAML file:

```bash
kubectl create -f secret.yaml
```

## How to Inject Secret into a Pod

There are several ways to inject Secret data into a Pod.

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
        - secretRef:
            name: my-secret
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
        - name: USERNAME
          valueFrom:
            secretKeyRef:
              name: my-secret
              key: username
        - name: PASSWORD
          valueFrom:
            secretKeyRef:
              name: my-secret
              key: password
```

### Mounting Secret as Volumes

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
    - name: my-container
      image: nginx
      volumeMounts:
        - name: secret-volume
          mountPath: /etc/secret
          readOnly: true
  volumes:
    - name: secret-volume
      secret:
        secretName: my-secret
```

## How to Get and Describe Secrets Using kubectl

Get Secrets

```bash
kubectl get secrets
```

Describe a Secret

```bash
kubectl describe secret my-secret
```
