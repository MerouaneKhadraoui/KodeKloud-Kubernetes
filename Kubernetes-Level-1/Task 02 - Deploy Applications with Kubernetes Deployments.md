# Task 02: Deploy Applications with Kubernetes Deployments

## Question

The Nautilus DevOps team is delving into Kubernetes for app management. One team member needs to create a deployment following these details:

**Task:**

Create a deployment named `nginx` to deploy the application `nginx` using the image `nginx:latest` (ensure to specify the tag)

`Note:` The `kubectl` utility on `jump_host` is set up to interact with the Kubernetes cluster.

---

## ✅ Option 1: Create deployment directly with `kubectl`

```bash
kubectl create deployment nginx --image=nginx:latest
```

---

## ✅ Option 2: Using a YAML manifest (preferred for exams/interviews)

Create a file named nginx-deployment.yaml:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:latest
```
Then apply it:

```bash
kubectl apply -f nginx-deployment.yaml
```

---

## ✅ Verify deployment

```bash
kubectl get deployments
kubectl get pods
```
You should see a deployment called nginx and at least one pod running with the `nginx:latest` image.