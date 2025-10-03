# Task 13: Deploy Highly Available Pods with ReplicationController

## Question

The Nautilus DevOps team is establishing a `ReplicationController` to deploy multiple pods for hosting applications that require a highly available infrastructure. Follow the specifications below to create the `ReplicationController`:

**Task:**

1. Create a `ReplicationController` using the `httpd` image with `latest` tag, and name it `httpd-replicationcontroller`.

2. Assign labels `app` as `httpd_app`, and `type` as `front-end`. Ensure the container is named `httpd-container` and set the replica count to `3`.

All `pods` should be running state post-deployment.

`Note:` The `kubectl` utility on `jump_host` is configured to operate with the Kubernetes cluster.

---

## Solution

1. **Create the YAML file**

Create a file called `httpd-replicationcontroller.yaml` with the following content:

```yaml
apiVersion: v1
kind: ReplicationController
metadata:
  name: httpd-replicationcontroller
spec:
  replicas: 3
  selector:
    app: httpd_app
    type: front-end
  template:
    metadata:
      name: httpd_app
      labels:
        app: httpd_app
        type: front-end
    spec:
      containers:
      - name: httpd-container
        image: httpd:latest
```

---

2. **Apply the YAML**

Run the command on the jump host:

```bash
kubectl apply -f httpd-replicationcontroller.yaml
```
---

3. **Verify the ReplicationController and Pods**

Check the ReplicationController:

```bash
kubectl get rc
```

Check that 3 pods are running:

```bash
kubectl get pods -l app=httpd_app
```
You should see **3 pods** created and running successfully.

---

✅ That’s it — you’ll have a **ReplicationController with 3 httpd pods** for high availability.