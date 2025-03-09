# 1. Troubleshooting "ImagePullBackOff" Error in Kubernetes

When you see an **"ImagePullBackOff"** error in Kubernetes, it means that your cluster is trying to pull a container image from a repository (like Docker Hub) but is failing. Kubernetes will **back off** (wait and retry) before trying again.

## Possible Reasons & Fixes

### 1. Incorrect Image Name or Tag
The image name or version might be wrong.

**Fix:** Double-check the image name and tag in your YAML file or Helm chart.

### 2. Image Doesn’t Exist in the Registry
The specified image might not be available in the registry.

**Fix:** Verify the image exists in the container registry (Docker Hub, AWS ECR, GCR, etc.).

### 3. Authentication Issues
If the image is private, Kubernetes might not have permission to pull it.

**Fix:** Create a **Secret** with proper credentials and link it to your pod using `imagePullSecrets`.

### 4. Network Issues
The cluster might not be able to reach the registry due to network problems.

**Fix:** Check internet connectivity, firewall rules, and proxy settings.

### 5. Rate Limiting by the Registry
Public registries (like Docker Hub) limit the number of pulls per hour.

**Fix:** Use a personal or organizational account with authentication to avoid rate limits.

## How to Debug?
Run these commands to check logs:

```sh
kubectl get pods -n <namespace>
kubectl describe pod <pod-name> -n <namespace>
kubectl get events -n <namespace>
```


# 2. Fixing "CreateContainerConfigError" in Kubernetes

## What is "CreateContainerConfigError"?

When you see a **CreateContainerConfigError** in Kubernetes, it means there is a problem with the container configuration. Kubernetes cannot start the container because something is missing or incorrect.

---
## Possible Reasons & Fixes

### 1️. Missing or Incorrect Environment Variables
Kubernetes needs some **environment variables** to start the container. If they are missing or incorrect, the container won't start.

**Fix:** Check your **Deployment YAML** file and make sure all required environment variables are set correctly.

```yaml
env:
  - name: DATABASE_URL
    value: "mysql://user:password@db:3306/mydb"
```

---

### 2️. Secret or ConfigMap Not Found
If your container depends on a **Secret** or **ConfigMap** that doesn't exist, it will fail to start.

**Fix:** Run the command below to check if the Secret or ConfigMap exists:

```sh
kubectl get secrets
kubectl get configmaps
```

If missing, create them or update your Deployment YAML.

---

### 3️. Issues with Volume Mounts
If a **volume** (file storage) is not correctly mounted, the container may fail.

**Fix:** Check the volume paths in your YAML file:

```yaml
volumeMounts:
  - name: config-volume
    mountPath: /app/config
volumes:
  - name: config-volume
    configMap:
      name: my-configmap
```

Make sure the **ConfigMap** or **PersistentVolume** exists.

---

### 4️. Incorrect Image or ImagePullSecrets Issue
If the container image is private or does not exist, Kubernetes won't be able to pull it.

**Fix:**
- Check the image name: `kubectl describe pod <pod-name>`
- If using a private registry, make sure **imagePullSecrets** is configured correctly.

```yaml
imagePullSecrets:
  - name: my-docker-secret
```

---

### 5️. Insufficient Permissions
Your Kubernetes service account may not have permission to access some resources.

**Fix:** Check the logs:

```sh
kubectl describe pod <pod-name>
kubectl get events
```

If there is a permissions issue, update the **Role** or **RoleBinding**.

---

## How to Debug?
Run these commands to check the error details:

```sh
kubectl get pods
kubectl describe pod <pod-name>
kubectl logs <pod-name>
```

 # 3.CreateContainerError - What It Means and How to Fix It

## What is CreateContainerError?
`CreateContainerError` is an error that happens when a container cannot be created in a Kubernetes or Docker environment. This means something is preventing the container from starting.

## Common Causes and Solutions

### 1. **Image Not Found**
**Cause:** The container image does not exist or the name is incorrect.
**Solution:**
- Check the image name and tag in your deployment file.
- Run `docker pull <image-name>` to see if the image exists.

### 2. **Insufficient Resources**
**Cause:** The node (server) does not have enough CPU or memory.
**Solution:**
- Check available resources using `kubectl describe node <node-name>`.
- Increase the resource limits in your deployment configuration.

### 3. **Incorrect Configuration**
**Cause:** There might be a misconfiguration in the YAML file.
**Solution:**
- Verify the deployment YAML file.
- Run `kubectl describe pod <pod-name>` to see more details.

### 4. **Permission Issues**
**Cause:** The container does not have permission to run certain commands.
**Solution:**
- Check if the user running the container has the required permissions.
- Use the correct service account in Kubernetes.

### 5. **Network Issues**
**Cause:** The container cannot access necessary networks.
**Solution:**
- Check network policies and firewall rules.
- Ensure that the container runtime can access the internet if needed.

## How to Debug
1. Run `kubectl get pods` to check the pod status.
2. Use `kubectl describe pod <pod-name>` to get more details.
3. Check container logs using `kubectl logs <pod-name> -c <container-name>`.
4. Look at events in the cluster with `kubectl get events`.


# 4.RunContainerError - What It Means and How to Fix It

## What is RunContainerError?
`RunContainerError` is an error that happens when a container is created but cannot start running. This means something is preventing the container from executing properly.

4. ## Common Causes and Solutions

### 1. **Invalid Command or Entry Point**
**Cause:** The container is trying to run an invalid command.
**Solution:** 
- Check the `CMD` or `ENTRYPOINT` in the Dockerfile.
- Run `docker run <image-name>` locally to test.

### 2. **Missing Files or Dependencies**
**Cause:** The container requires files or dependencies that are missing.
**Solution:**
- Ensure all required files and libraries are inside the container.
- Check the Dockerfile and entry scripts for missing dependencies.

### 3. **Permission Issues**
**Cause:** The container does not have permission to execute the required files.
**Solution:**
- Check file permissions using `ls -l`.
- Use `chmod +x <file>` to make scripts executable.

### 4. **Insufficient Resources**
**Cause:** The node (server) does not have enough CPU or memory.
**Solution:**
- Check available resources using `kubectl describe node <node-name>`.
- Increase the resource limits in your deployment configuration.

### 5. **Networking Issues**
**Cause:** The container cannot connect to necessary services or databases.
**Solution:**
- Check environment variables and connection strings.
- Ensure the correct network settings are applied.

## How to Debug
1. Run `kubectl get pods` to check the pod status.
2. Use `kubectl describe pod <pod-name>` to get more details.
3. Check container logs using `kubectl logs <pod-name> -c <container-name>`.
4. Look at events in the cluster with `kubectl get events`.


# 5.CrashLoopBackOff - What It Means and How to Fix It

## What is CrashLoopBackOff?
`CrashLoopBackOff` is an error that happens when a container keeps crashing and Kubernetes is waiting before restarting it again. This means something inside the container is failing repeatedly.

## Common Causes and Solutions

### 1. **Application Crash**
**Cause:** The application inside the container is failing.
**Solution:** 
- Check logs using `kubectl logs <pod-name> -c <container-name>`.
- Fix any application errors.

### 2. **Invalid Command or Entry Point**
**Cause:** The container is trying to run an invalid command.
**Solution:**
- Check the `CMD` or `ENTRYPOINT` in the Dockerfile.
- Run `docker run <image-name>` locally to test.

### 3. **Missing Files or Dependencies**
**Cause:** The container requires files or dependencies that are missing.
**Solution:**
- Ensure all required files and libraries are inside the container.
- Check the Dockerfile and entry scripts for missing dependencies.

### 4. **Permission Issues**
**Cause:** The container does not have permission to execute the required files.
**Solution:**
- Check file permissions using `ls -l`.
- Use `chmod +x <file>` to make scripts executable.

### 5. **Insufficient Resources**
**Cause:** The node (server) does not have enough CPU or memory.
**Solution:**
- Check available resources using `kubectl describe node <node-name>`.
- Increase the resource limits in your deployment configuration.

### 6. **Crash Due to Readiness/Liveness Probe Failure**
**Cause:** The health check probes are failing, causing Kubernetes to restart the container.
**Solution:**
- Check probe settings in the deployment YAML.
- Run `kubectl describe pod <pod-name>` to see probe failures.

### 7. **Networking Issues**
**Cause:** The container cannot connect to necessary services or databases.
**Solution:**
- Check environment variables and connection strings.
- Ensure the correct network settings are applied.

## How to Debug
1. Run `kubectl get pods` to check the pod status.
2. Use `kubectl describe pod <pod-name>` to get more details.
3. Check container logs using `kubectl logs <pod-name> -c <container-name>`.
4. Look at events in the cluster with `kubectl get events`.
5. If needed, increase restart delays using `restartPolicy` settings.


# 6.OOMKilled - What It Means and How to Fix It

## What is OOMKilled?
`OOMKilled` is an error that happens when a container is using too much memory, and the system forcefully stops (kills) it. This means the container exceeded its memory limit.

## Common Causes and Solutions

### 1. **Application Using Too Much Memory**
**Cause:** The application is consuming more memory than available.
**Solution:**
- Optimize your application’s memory usage.
- Use memory-efficient data structures.

### 2. **Memory Limits Are Too Low**
**Cause:** The container has strict memory limits.
**Solution:**
- Increase memory limits in the deployment YAML file:
  ```yaml
  resources:
    limits:
      memory: "512Mi"  # Increase this value
  ```
- Run `kubectl describe pod <pod-name>` to check memory usage.

### 3. **Memory Leak in Application**
**Cause:** The application keeps using more and more memory over time.
**Solution:**
- Identify memory leaks using monitoring tools like Prometheus or Grafana.
- Fix memory leaks in your application code.

### 4. **Multiple Processes Running in a Single Container**
**Cause:** Several processes inside the container are consuming memory.
**Solution:**
- Use a process manager like `supervisord` if multiple processes are needed.
- Ensure only necessary processes run inside the container.

### 5. **Node (Server) Running Out of Memory**
**Cause:** The node does not have enough memory for all running containers.
**Solution:**
- Check node memory with `kubectl describe node <node-name>`.
- Move workloads to other nodes or upgrade hardware.

## How to Debug
1. Run `kubectl get pods` to check pod status.
2. Use `kubectl describe pod <pod-name>` to see OOMKilled details.
3. Check container logs using `kubectl logs <pod-name> -c <container-name>`.
4. Monitor memory usage with `kubectl top pod`.
5. Adjust memory limits in the deployment YAML.


# 7.Node Disk Pressure - What It Means and How to Fix It

## What is Node Disk Pressure?
`Node Disk Pressure` is an error that happens when a Kubernetes node is running low on disk space. This means the node cannot schedule new pods or may evict existing ones to free up space.

## Common Causes and Solutions

### 1. **Logs Taking Up Too Much Space**
**Cause:** Container logs are using too much disk space.
**Solution:**
- Clear old logs using `journalctl --vacuum-time=1d`.
- Configure log rotation to prevent excessive logging.

### 2. **Large or Unused Docker Images**
**Cause:** Old or unused container images are filling up disk space.
**Solution:**
- Run `docker system prune -a` to remove unused images.
- Use smaller base images (e.g., `alpine` instead of `ubuntu`).

### 3. **Too Many Unused Containers**
**Cause:** Stopped containers are taking up disk space.
**Solution:**
- Run `docker ps -a` to check stopped containers.
- Remove them using `docker rm $(docker ps -aq)`.

### 4. **Ephemeral Storage Usage is High**
**Cause:** Applications are writing too much temporary data inside the container.
**Solution:**
- Use persistent volumes instead of writing to `/tmp`.
- Set storage limits in the deployment YAML:
  ```yaml
  resources:
    limits:
      ephemeral-storage: "2Gi"  # Adjust as needed
  ```

### 5. **Node Disk is Almost Full**
**Cause:** The node itself is running out of disk space.
**Solution:**
- Check disk usage with `df -h`.
- Clean up unnecessary files or increase disk size.

## How to Debug
1. Run `kubectl get nodes` to check node status.
2. Use `kubectl describe node <node-name>` to see disk pressure details.
3. Check disk usage with `df -h` and `du -sh /var/lib/docker`.
4. Remove unnecessary files, logs, or images.
5. Restart the node if necessary to free up space.


# 8.Node Not Ready - What It Means and How to Fix It

## What is Node Not Ready?
`Node Not Ready` is an error that happens when a Kubernetes node is unable to run workloads. This means the node is facing issues and cannot schedule new pods.

## Common Causes and Solutions

### 1. **Network Issues**
**Cause:** The node cannot communicate with the Kubernetes cluster.
**Solution:**
- Check network connectivity using `ping <master-node-ip>`.
- Restart the network service with `systemctl restart network`.

### 2. **Node Disk Pressure**
**Cause:** The node is running low on disk space.
**Solution:**
- Run `df -h` to check disk usage.
- Free up space by removing unused logs, images, and containers.

### 3. **Insufficient CPU or Memory**
**Cause:** The node does not have enough resources.
**Solution:**
- Run `kubectl describe node <node-name>` to check resource usage.
- Free up resources by moving workloads to other nodes.

### 4. **Kubelet is Down**
**Cause:** The Kubelet service is not running on the node.
**Solution:**
- Restart the service using `systemctl restart kubelet`.
- Check logs using `journalctl -u kubelet -n 50`.

### 5. **Docker or Container Runtime Issues**
**Cause:** The container runtime (Docker, containerd) is not working.
**Solution:**
- Restart Docker using `systemctl restart docker`.
- Check container logs using `docker ps -a` and `docker logs <container-id>`.

### 6. **Control Plane Connection Issues**
**Cause:** The node cannot reach the Kubernetes API server.
**Solution:**
- Run `kubectl get nodes` from another node to check cluster status.
- Restart the node if necessary.

## How to Debug
1. Run `kubectl get nodes` to check node status.
2. Use `kubectl describe node <node-name>` to get more details.
3. Check system logs with `journalctl -u kubelet -n 50`.
4. Restart services like Kubelet, Docker, or the network.
5. If needed, reboot the node.


# Kubernetes Troubleshooting Guide

## 1. What is Kubernetes Troubleshooting?
Kubernetes troubleshooting helps identify and fix problems with pods, containers, and nodes to keep applications running smoothly.

## 3. Must-Know Kubernetes Troubleshooting Commands

| Command | What It Does |
|---------|-------------|
| `kubectl get pods` | Shows all pods in the current namespace and their status. |
| `kubectl describe pod <pod-name>` | Gives detailed information about a specific pod, including errors. |
| `kubectl logs <pod-name>` | Shows logs from a running pod’s main container. |
| `kubectl logs -f <pod-name>` | Streams real-time logs from a pod’s main container. |
| `kubectl logs <pod-name> -c <container-name>` | Fetches logs from a specific container inside a pod. |
| `kubectl exec -it <pod-name> -- /bin/sh` | Opens a terminal inside a running container for debugging. |
| `kubectl get events --sort-by=.metadata.creationTimestamp` | Lists Kubernetes events sorted by time to diagnose issues. |
| `kubectl get nodes -o wide` | Shows nodes with details like IPs and roles. |
| `kubectl get all -n <namespace>` | Retrieves all resources in a specific namespace. |
| `kubectl describe node <node-name>` | Gives detailed information about a node, including health status. |
| `kubectl get deployments -o wide` | Lists deployments with details like image versions. |
| `kubectl get svc -o wide` | Shows all services with their IPs and external endpoints. |
| `kubectl top pod` | Displays CPU and memory usage of pods (requires metrics-server). |
| `kubectl top node` | Shows CPU and memory usage of nodes. |
| `kubectl get pvc` | Lists persistent volume claims and their status. |
| `kubectl get pv` | Shows persistent volumes and their details. |
| `kubectl describe ingress <ingress-name>` | Provides details about an ingress resource for debugging. |
| `kubectl debug node/<node-name> --image=img_v1.0` | Creates a debugging pod on a node using a specific image. |
| `kubectl cordon <node-name>` | Prevents new pods from being scheduled on a node. |












