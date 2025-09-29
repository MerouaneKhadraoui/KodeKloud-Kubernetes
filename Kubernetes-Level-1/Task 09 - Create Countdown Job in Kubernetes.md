# Task 09: Create Countdown Job in Kubernetes

## Question

The Nautilus DevOps team is crafting jobs in the Kubernetes cluster. While they're developing actual scripts/commands, they're currently setting up templates and testing jobs with dummy commands. Please create a job template as per details given below:

**Task:**

1. Create a job named `countdown-devops`.

2. The spec template should be named `countdown-devops` (under metadata), and the container should be named `container-countdown-devops`

3. Utilize image `fedora` with `latest` tag (ensure to specify as `fedora:latest`), and set the restart policy to `Never`.

4. Execute the command `sleep 5`

`Note:` The `kubectl` utility on `jump_host` is configured to operate with the Kubernetes cluster.

---

## Solution

1. **Create the YAML file**

Save this as `countdown-devops-job.yaml`:

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: countdown-devops
spec:
  template:
    metadata:
      name: countdown-devops
    spec:
      containers:
      - name: container-countdown-devops
        image: fedora:latest
        command: ["sleep", "5"]
      restartPolicy: Never
```
---

2. **Apply the job**

```bash
kubectl apply -f countdown-devops-job.yaml
```
---

3. **Verify the job**

```bash
kubectl get jobs
kubectl get pods
```
You should see the pod run, sleep for 5 seconds, then complete ✅.