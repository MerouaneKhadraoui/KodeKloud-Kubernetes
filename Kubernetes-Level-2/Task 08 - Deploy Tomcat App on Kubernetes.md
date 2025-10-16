# Task 08: Deploy Tomcat App on Kubernetes

## Question

A new java-based application is ready to be deployed on a Kubernetes cluster. The development team had a meeting with the DevOps team to share the requirements and application scope. The team is ready to setup an application stack for it under their existing cluster. Below you can find the details for this:

**Task:**

1. Create a namespace named `tomcat-namespace-devops`.

2. Create a `deployment` for tomcat app which should be named as `tomcat-deployment-devops` under the same namespace you created. Replica count should be `1`, the container should be named as `tomcat-container-devops`, its image should be `gcr.io/kodekloud/centos-ssh-enabled:tomcat` and its container port should be `8080`.

3. Create a `service` for tomcat app which should be named as `tomcat-service-devops` under the same namespace you created. Service type should be `NodePort` and nodePort should be `32227`.

Before clicking on `Check` button please make sure the application is up and running.

`You can use any labels as per your choice.`

`Note:` The `kubectl` utility on `jump_host` is configured to operate with the Kubernetes cluster.

---

## Solution

## 🧩 Step 1: Create the Namespace

```bash
kubectl create namespace tomcat-namespace-devops
```

## 🏗️ Step 2: Create the Tomcat Deployment

Run this on the `jump_host`:

```bash
vi tomcat-deployment.yaml
```

Paste the following content:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: tomcat-deployment-devops
  namespace: tomcat-namespace-devops
  labels:
    app: tomcat-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: tomcat-app
  template:
    metadata:
      labels:
        app: tomcat-app
    spec:
      containers:
      - name: tomcat-container-devops
        image: gcr.io/kodekloud/centos-ssh-enabled:tomcat
        ports:
        - containerPort: 8080
```
Apply it:

```bash
kubectl apply -f tomcat-deployment.yaml
```

---

## 🌐 Step 3: Create the Tomcat Service

**tomcat-service.yaml**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: tomcat-service-devops
  namespace: tomcat-namespace-devops
  labels:
    app: tomcat-app
spec:
  type: NodePort
  selector:
    app: tomcat-app
  ports:
  - port: 8080
    targetPort: 8080
    nodePort: 32227
```
Apply it:

```bash
kubectl apply -f tomcat-service.yaml
```

---

## ✅ Step 4: Verify Everything

Check resources under the namespace:

```bash
kubectl get all -n tomcat-namespace-devops
```
You should see:

- 1 **Pod** running from the deployment
- 1 **Service** of type NodePort (32227)

Check pod logs (optional):
```bash
kubectl logs -n tomcat-namespace-devops -l app=tomcat-app
```
---

## ✅ Quick Command Alternative (without YAML)

If you prefer to do it all inline:

```bash
kubectl create namespace tomcat-namespace-devops

kubectl create deployment tomcat-deployment-devops \
  --image=gcr.io/kodekloud/centos-ssh-enabled:tomcat \
  --replicas=1 -n tomcat-namespace-devops \
  --port=8080

kubectl expose deployment tomcat-deployment-devops \
  --name=tomcat-service-devops \
  --type=NodePort \
  --port=8080 \
  --target-port=8080 \
  --node-port=32227 \
  -n tomcat-namespace-devops
```
---

## 🧠 Tip:

Once done, check the Tomcat app from your node’s IP:

```bash
http://<node-ip>:32227
```