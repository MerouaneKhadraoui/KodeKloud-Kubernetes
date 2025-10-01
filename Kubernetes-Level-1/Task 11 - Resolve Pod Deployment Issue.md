# Task 11: Resolve Pod Deployment Issue

## Question

A junior DevOps team member encountered difficulties deploying a stack on the Kubernetes cluster. The pod fails to start, presenting errors. Let's troubleshoot and rectify the issue promptly.

**Task:**

1. There is a pod named `webserver`, and the container within it is named `nginx-container`, its utilizing the `nginx:latest` image.

2. Additionally, there's a sidecar container named `sidecar-container` using the `ubuntu:latest` image.

Identify and address the issue to ensure the pod is in the `running` state and the application is accessible.

`Note:` The `kubectl` utility on `jump_host` is set up to interact with the Kubernetes cluster.

```bash
kuectl describe pod webserver
```
The Result is :

```yaml
Containers:
  nginx-container:
    Container ID:   
    Image: nginx:latests
```

---

## Issue Analysis

From the `kubectl describe pod webserver` output:

```yaml
Containers:
  nginx-container:
    Container ID:   
    Image: nginx:latests
```
The image name is written as `nginx:latests` instead of `nginx:latest`.
That typo prevents Kubernetes from pulling the correct image, so the pod cannot start.

## Solution

1. **Check the existing pod YAML**

Get the manifest of the pod:

```bash
kubectl get pod webserver -o yaml > /tmp/webserver.yaml
```

2. **Edit the YAML**

```yaml
containers:
- name: nginx-container
  image: nginx:latest   # ✅ corrected
- name: sidecar-container
  image: ubuntu:latest
```

3. **Delete the faulty pod**:

Since pods are immutable in terms of image name changes

```bash
kubectl delete pod webserver
```

4. **Recreate the pod with the fixed YAML**

```bash
kubectl apply -f /tmp/webserver.yaml
```
---

5. **Verify pod status**

```bash
kubectl get pods
kubectl describe pod webserver
```
You should now see the pod in `Running` state.

---

6. **Test accessibility**

```bash
kubectl port-forward pod/webserver 8080:80
curl http://localhost:8080
```
---

## ✅ Final Answer:

The issue is a typo in the image name (`nginx:latests`). Correct it to `nginx:latest`, delete the bad pod, and redeploy. The pod will then start successfully with both containers running.