# Task 01: Deploy Pods in Kubernetes Cluster

## Question

The Nautilus DevOps team is diving into Kubernetes for application management. One team member has a task to create a pod according to the details below:

**Task:**

1. Create a pod named `pod-httpd` using the `httpd` image with the `latest` tag. Ensure to specify the tag as `httpd:latest`.

2. Set the `app` label to `httpd_app`, and name the container as `httpd-container`.

`Note:` The `kubectl` utility on `jump_host` is configured to operate with the Kubernetes cluster.

---

## Solution

1. **Create a Pod manifest file**

You can create a YAML file (recommended way):

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-httpd
  labels:
    app: httpd_app
spec:
  containers:
    - name: httpd-container
      image: httpd:latest
```
Save it as pod-httpd.yaml.

---

2. **Apply the manifest**

```bash
kubectl apply -f pod-httpd.yaml
```

---

3. **Verify pod creation**

```bash
kubectl get pods -o wide
kubectl describe pod pod-httpd
```

---

👉 That’s it! Your pod `pod-httpd` with container `httpd-container` and label `app=httpd_app` will be running in the cluster.