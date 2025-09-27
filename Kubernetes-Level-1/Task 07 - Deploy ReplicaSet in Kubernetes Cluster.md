# Task 07: Deploy ReplicaSet in Kubernetes Cluster

## Question

The Nautilus DevOps team is gearing up to deploy applications on a Kubernetes cluster for migration purposes. A team member has been tasked with creating a ReplicaSet outlined below:

**Task:**

1. Create a ReplicaSet using `nginx` image with `latest` tag (ensure to specify as `nginx:latest`) and name it `nginx-replicaset`.

2. Apply labels: `app` as `nginx_app`, `type` as `front-end`.

3. Name the container `nginx-container`. Ensure the replica count is `4`.

`Note:` The `kubectl` utility on `jump_host` is configured to operate with the Kubernetes cluster.

---

## Solution

1. **Create the YAML file**

Run this on the `jump_host`:

```bash
vi nginx-replicaset.yaml
```

Paste the following content:

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx-replicaset
  labels:
    app: nginx_app
    type: front-end
spec:
  replicas: 4
  selector:
    matchLabels:
      app: nginx_app
      type: front-end
  template:
    metadata:
      labels:
        app: nginx_app
        type: front-end
    spec:
      containers:
      - name: nginx-container
        image: nginx:latest
```

---

2. **Apply the ReplicaSet**

```bash
kubectl apply -f nginx-replicaset.yaml
```

---

3. **Verify**

Check if the ReplicaSet and pods are running:

```bash
kubectl get replicaset 
# or
k get rs
kubectl get pods -o wide
```
You should see **4 pods** created with the correct labels.

---

👉 This manifest meets all requirements:

- **Name**: `nginx-replicaset`
- **Container name**: `nginx-container`
- **Image**: `nginx:latest`
- **Replicas**: 4
- **Labels**: `app=nginx_app`, `type=front-end`