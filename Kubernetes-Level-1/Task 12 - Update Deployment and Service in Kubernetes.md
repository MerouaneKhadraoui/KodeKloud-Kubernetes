# Task 12: Update Deployment and Service in Kubernetes

## Question

An application deployed on the Kubernetes cluster requires an update with new features developed by the Nautilus application development team. The existing setup includes a deployment named `nginx-deployment` and a service named `nginx-service`. Below are the necessary changes to be implemented without deleting the deployment and service:

**Task:**

1. Modify the service nodeport from `30008` to `32165`

2. Change the replicas count from `1` to `5`

3. Update the image from `nginx:1.18` to `nginx:latest`

`Note:` The `kubectl` utility on `jump_host` is configured to operate with the Kubernetes cluster.

---

## Solution

1. **Change the Service NodePort**

```bash
kubectl edit svc nginx-service
```

Inside the editor, look for:

```yaml
ports:
  - port: 80
    targetPort: 80
    nodePort: 30008   # <-- change this
```
Change `30008` to `32165`:

```yaml
    nodePort: 32165
```
Save & exit.

Alternatively (one-liner patch):

```yaml
kubectl patch svc nginx-service -p '{"spec": {"ports": [{"port": 80, "targetPort": 80, "nodePort": 32165}]}}'
```

---

2. **Scale Deployment Replicas**

```bash
kubectl scale deployment nginx-deployment --replicas=5
```
Verify:

```bash
kubectl get deploy nginx-deployment
```
---

3. **Update Image**

```bash
kubectl set image deployment/nginx-deployment nginx=nginx:latest
```
***(Assuming the container name is `nginx` inside the pod template. If unsure, check with `kubectl describe deploy nginx-deployment`.)***

Verify:

```bash
kubectl rollout status deployment/nginx-deployment
kubectl get pods -o wide
```
---

## 🔍 Final Verification

```bash
kubectl get svc nginx-service
kubectl get deploy nginx-deployment
kubectl describe deploy nginx-deployment | grep Image
```
You should see:

- `nodePort: 32165`
- `Replicas: 5`
- `Image: nginx:latest`