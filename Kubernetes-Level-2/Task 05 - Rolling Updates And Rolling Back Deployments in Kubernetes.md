# Task 05: Rolling Updates And Rolling Back Deployments in Kubernetes

## Question

There is a production deployment planned for next week. The Nautilus DevOps team wants to test the deployment update and rollback on Dev environment first so that they can identify the risks in advance. Below you can find more details about the plan they want to execute.

**Task:**

1. Create a namespace `devops`. Create a deployment called `httpd-deploy` under this new namespace, It should have one container called `httpd`, use `httpd:2.4.28` image and `2` replicas. The deployment should use `RollingUpdate` strategy with `maxSurge=1`, and `maxUnavailable=2`. Also create a `NodePort` type service named `httpd-service` and expose the deployment on `nodePort: 30008`.

2. Now upgrade the deployment to version `httpd:2.4.43` using a rolling update.

3. Finally, once all pods are updated undo the recent update and roll back to the previous/original version.

`Note:` 

a. The `kubectl` utility on `jump_host` is set up to interact with the Kubernetes cluster.

b. Please make sure you only use the specified image(s) for this deployment and as per the sequence mentioned in the task description. If you mistakenly use a wrong image and fix it later, that will also distort the revision history which can eventually fail this task.

---

## 🧩 Step-by-Step Solution

1. **Create Namespace**

```bash
kubectl create namespace devops
```

---

2. **Create the Deployment**

We’ll create a deployment named `httpd-deploy` with:

- Namespace: `devops`
- Image: `httpd:2.4.28`
- Replicas: `2`
- Strategy: RollingUpdate with `maxSurge=1` and `maxUnavailable=2`

You can use `kubectl create deployment` with YAML patching, but for full control, let’s use a manifest.

Create the file `httpd-deploy.yaml`:

```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: httpd-deploy
  namespace: devops
spec:
  replicas: 2
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 2
  selector:
    matchLabels:
      app: httpd
  template:
    metadata:
      labels:
        app: httpd
    spec:
      containers:
      - name: httpd
        image: httpd:2.4.28
        ports:
        - containerPort: 80
```
Apply it:

```bash
kubectl apply -f httpd-deploy.yaml
```
Verify:

```bash
kubectl get deployments -n devops
kubectl get pods -n devops
```

---

3. **Create the Service**

Expose the deployment with a NodePort service on port `30008`.

```bash
kubectl expose deployment httpd-deploy \
  --type=NodePort \
  --name=httpd-service \
  --port=80 \
  --target-port=80 \
  -n devops
```
Check:

```bash
kubectl get svc -n devops
```
---

4. **Perform the Rolling Update**

Now, upgrade the deployment image to version `2.4.43`.

```bash
kubectl set image deployment/httpd-deploy httpd=httpd:2.4.43 -n devops
```
Monitor the rollout:
```bash
kubectl rollout status deployment/httpd-deploy -n devops
```
Confirm update:
```bash
kubectl get pods -n devops -o wide
kubectl describe deployment httpd-deploy -n devops | grep Image
```
Expected output:
```bash
Image: httpd:2.4.43
```
---

5. **Roll Back to the Previous Version**

Now undo the update and roll back to the previous image (`httpd:2.4.28`).

```bash
kubectl rollout undo deployment/httpd-deploy -n devops
```
Verify rollback:

```bash
kubectl rollout status deployment/httpd-deploy -n devops
kubectl describe deployment httpd-deploy -n devops | grep Image
```
You should again see:
```bash
Image: httpd:2.4.28
```
---

## ✅ Final Verification

Check the whole thing:

```bash
kubectl get all -n devops
```

You should see:

- `2` pods running (`httpd:2.4.28`)
- `httpd-service` of type `NodePort` on port `30008`
- Deployment and ReplicaSet objects under the `devops` namespace.

---

## 🧠 Notes & Tips

- The **revision history** is crucial here. If you used a wrong image and fixed it later, it creates extra revisions — that’s why you must follow the exact order and use only the images `2.4.28` → `2.4.43` → rollback.
- Use `kubectl rollout history deployment/httpd-deploy -n devops` to confirm revisions.