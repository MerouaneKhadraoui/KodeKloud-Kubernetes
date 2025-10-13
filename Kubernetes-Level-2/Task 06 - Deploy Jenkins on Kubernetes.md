# Task 06: Deploy Jenkins on Kubernetes

## Question

The Nautilus DevOps team is planning to set up a Jenkins CI server to create/manage some deployment pipelines for some of the projects. They want to set up the Jenkins server on Kubernetes cluster. Below you can find more details about the task:

**Task:**

1) Create a namespace `jenkins`

2) Create a Service for jenkins deployment. Service name should be `jenkins-service` under `jenkins` namespace, type should be `NodePort`, nodePort should be `30008`

3) Create a Jenkins Deployment under `jenkins` namespace, It should be name as `jenkins-deployment` , labels app should be `jenkins` , container name should be `jenkins-container` , use `jenkins/jenkins` image , containerPort should be `8080` and replicas count should be `1`.


Make sure to wait for the pods to be in running state and make sure you are able to access the Jenkins login screen in the browser before hitting the `Check` button.

`Note:` The `kubectl` utility on `jump_host` is set up to interact with the Kubernetes cluster.

---

## Solution

1. **Create the namespace**

```bash
kubectl create namespace jenkins
```
You can verify it:

```bash
kubectl get ns
```

2. **Create the Deployment YAML file**

Let’s create a file named **jenkins-deployment.yaml**:

```bash
vi jenkins-deployment.yaml
#or
k create deploy jenkins-deployment -n jenkins --image=jenkins/jenkins -r 1 --port=8080 --dry-run=client -o yaml > jenkins-deployment.yaml
```
Paste the above content and save.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: jenkins-deployment
  namespace: jenkins
  labels:
    app: jenkins
spec:
  replicas: 1
  selector:
    matchLabels:
      app: jenkins
  template:
    metadata:
      labels:
        app: jenkins
    spec:
      containers:
      - name: jenkins-container
        image: jenkins/jenkins
        ports:
        - containerPort: 8080
```
Apply it:

```bash
kubectl apply -f jenkins-deployment.yaml
```

Check that the pod is running:

```bash
kubectl get pods -n jenkins
```
---

3. **Create the Service YAML file**

```bash
vi nginx-service.yaml
#or
kubectl expose deploy jenkins-deployment -n jenkins --name=jenkins-service --type=NodePort --port=8080 --dry-run=client -o yaml > nginx-service.yaml
```
Paste the above content and save.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: jenkins-service
  namespace: jenkins
spec:
  type: NodePort
  selector:
    app: jenkins
  ports:
    - port: 8080
      targetPort: 8080
      nodePort: 30008
```
Apply it:

```bash
kubectl apply -f jenkins-service.yaml
```
Verify the service:

```bash
kubectl get svc -n jenkins
```
You should see something like:

```pgsql
NAME              TYPE       CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
jenkins-service   NodePort   10.96.x.x      <none>        8080:30008/TCP   10s
```

---

4. **Verify access**

Wait until the pod is **Running**:

```bash
kubectl get pods -n jenkins -w
```
Once it’s ready, open Jenkins in your browser:

```ccp
http://<NodeIP>:30008
```
You should now see the **Jenkins login/unlock screen** 🎉

---

5. **(Optional) Quick check commands**

```bash
kubectl get all -n jenkins
```
Output should show:

- 1 deployment (`jenkins-deployment`)
- 1 pod (`Running`)
- 1 service (`jenkins-service`)