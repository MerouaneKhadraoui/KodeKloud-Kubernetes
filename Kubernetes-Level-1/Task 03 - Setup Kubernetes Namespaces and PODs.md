# Task 03: Setup Kubernetes Namespaces and PODs

## Question

The Nautilus DevOps team is planning to deploy some micro services on Kubernetes platform. The team has already set up a Kubernetes cluster and now they want to set up some namespaces, deployments etc. Based on the current requirements, the team has shared some details as below:

**Task:**

Create a namespace named `dev` and deploy a POD within it. Name the pod `dev-nginx-pod` and use the `nginx` image with the `latest` tag. Ensure to specify the tag as `nginx:latest`.

`Note:` The `kubectl` utility on `jump_host` is configured to operate with the Kubernetes cluster.

---

## Solution

1. **Create the namespace**

```bash
kubectl create namespace dev
```

2. **Create the Pod manifest file**

Create a YAML file called `dev-nginx-pod.yaml` with the following content:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: dev-nginx-pod
  namespace: dev
spec:
  containers:
  - name: nginx-container
    image: nginx:latest
```
Save it as pod-httpd.yaml.

---

3. **Apply the Pod**

```bash
kubectl apply -f dev-nginx-pod.yaml
```

---

4. **Verify**

Check that the namespace and Pod are created successfully:

```bash
kubectl get ns
kubectl get pods -n dev
```
You should see `dev-nginx-pod` running inside the `dev` namespace. ✅

---

👉 If KodeKloud expects a direct kubectl run (without YAML), you can also do it in one line:

```bash
kubectl run dev-nginx-pod --image=nginx:latest -n dev
```
(but YAML is usually the preferred way in real DevOps practice).