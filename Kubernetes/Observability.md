# Observability

## Readiness and Liveness Probes

### Readiness Probes

Readiness Probes are used to determine if a Pod is ready to accept traffic. They ensure that the application within the container is fully initialized and ready to serve requests. This prevents the application from receiving traffic before it is ready. For example, when containers in our Pods are spun up, the application within might not be ready to accept requests immediately. Readiness Probes help us prevent this behavior by checking the readiness state before routing traffic to the Pod.

### Liveness Probes

Liveness Probes are used to check if an application is running as expected. They detect situations where the application has gone into a non-responsive state due to bugs or other issues. Kubernetes can then restart the container to recover from the failure. Liveness Probes help us maintain the application's availability by ensuring it is alive and functional.

### Types of Probes

We can configure probes using different methods:

- **HTTP Probes**: Use an HTTP GET request to check the health of the application.
- **TCP Probes**: Attempt to open a TCP connection to the specified port.
- **gRPC Probes**: Use gRPC health checks.
- **Exec Command Probes**: Run a specified command inside the container and check the exit status.

### Example Configurations

#### Readiness Probe Example

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: readiness-example
spec:
  containers:
    - name: myapp
      image: myapp:latest
      readinessProbe:
        httpGet:
          path: /health
          port: 8080
        initialDelaySeconds: 5
        periodSeconds: 10
```

#### Liveness Probe Example

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: liveness-example
spec:
  containers:
    - name: myapp
      image: myapp:latest
      livenessProbe:
        httpGet:
          path: /health
          port: 8080
        initialDelaySeconds: 15
        periodSeconds: 20
```

### Additional Information

- **Initial Delay**: `initialDelaySeconds` is the number of seconds after the container has started before the probe is initiated.
- **Period**: `periodSeconds` is the frequency (in seconds) at which the probe is performed.
- **Failure Threshold**: `failureThreshold` is the number of times a probe can fail before the container is restarted or marked as not ready.
- **Success Threshold**: `successThreshold` is the number of times a probe must succeed to be considered successful after having failed.

Readiness and Liveness Probes are essential for maintaining the stability and availability of your applications in Kubernetes. They provide mechanisms to ensure that applications are only accessible when they are ready and can recover from failure states automatically.

## Logs

We can check logs for our Pods. This can be useful for debugging unwanted behaviors.

### Viewing Logs

#### Single Container Pod

The following command returns a snapshot of the logs for a single container Pod:

```bash
kubectl logs pod_name
```

#### Multi-Container Pod

The following command returns a snapshot of the logs for a specific container in a multi-container Pod:

```bash
kubectl logs -c container_name pod_name
```

#### Live Logs

The following command returns live logs of your Pod (streaming logs):

```bash
kubectl logs -f -c container_name pod_name
```

#### Previous Instance Logs

To get the logs of a previously terminated container instance in a Pod, you can use the --previous flag:

```bash
kubectl logs -p pod_name
```

#### All Containers in a Pod

To get logs from all containers in a Pod, use the --all-containers flag:

```bash
kubectl logs pod_name --all-containers=true
```

#### Log Tail

To see the last few lines of the log, use the --tail flag followed by the number of lines:

```bash
kubectl logs pod_name --tail=10
```
