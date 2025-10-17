# Task 09: Deploy Node App on Kubernetes

## Question

The Nautilus development team has completed development of one of the node applications, which they are planning to deploy on a Kubernetes cluster. They recently had a meeting with the DevOps team to share their requirements. Based on that, the DevOps team has listed out the exact requirements to deploy the app. Find below more details:

**Task:**

1. Create a deployment using `gcr.io/kodekloud/centos-ssh-enabled:node` image, replica count must be `2`.

2. Create a service to expose this app, the service type must be `NodePort`, targetPort must be `8080` and nodePort should be `30012`.

3. Make sure all the pods are in `Running` state after the deployment.

4. You can check the application by clicking on `NodeApp` button on top bar.

`You can use any labels as per your choice.`

`Note:` The `kubectl` utility on `jump_host` is configured to operate with the Kubernetes cluster.

---

## Solution

## ✅ Quick Command Alternative (without YAML)

If you prefer to do it all inline:

```bash
kubectl create deployment nodeapp \
  --image=gcr.io/kodekloud/centos-ssh-enabled:node \
  --replicas=2 \
  --port=8080

kubectl expose deployment nodeapp \
  --type=NodePort \
  --port=8080

kubectl edit svc nodeapp
# change nodePort: 30012
```
