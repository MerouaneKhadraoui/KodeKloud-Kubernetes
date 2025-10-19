# Task 01: Deploy Apache Web Server on Kubernetes CLuster

## Question

There is an application that needs to be deployed on Kubernetes cluster under Apache web server. The Nautilus application development team has asked the DevOps team to deploy it. We need to develop a template as per requirements mentioned below:

**Task:**

1. Create a `namespace` named as `httpd-namespace-nautilus`.

2. Create a `deployment` named as `httpd-deployment-nautilus` under newly created namespace. For the deployment use `httpd` image with `latest` tag only and remember to mention the tag i.e `httpd:latest`, and make sure replica counts are `2`.

3. Create a `service` named as `httpd-service-nautilus` under same namespace to expose the deployment, nodePort should be `30004`.

`Note:` The `kubectl` utility on `jump_host` is configured to operate with the Kubernetes cluster.

---

## Solution

## ✅ Quick Command Alternative (without YAML)

If you prefer to do it all inline:

```bash
kubectl create ns httpd-namespace-nautilus

kubectl create deployment httpd-deployment-nautilus \
  -n httpd-namespace-nautilus \
  --image=httpd:latest \
  --port=80 \
  -r 2

kubectl expose deployment httpd-deployment-nautilus \
  -n httpd-namespace-nautilus \
  --name=httpd-service-nautilus \
  --type=NodePort \
  --port=80

kubectl edit svc httpd-service-nautilus -n httpd-namespace-nautilus
# change nodePort: 30004
```
