# Task 08: Schedule Cronjobs in Kubernetes

## Question

The Nautilus DevOps team is setting up recurring tasks on different schedules. Currently, they're developing scripts to be executed periodically. To kickstart the process, they're creating cron jobs in the Kubernetes cluster with placeholder commands. Follow the instructions below:

**Task:**

1. Create a cronjob named `nautilus`.

2. Set Its schedule to something like `*/12 * * * *`. You can set any schedule for now.

3. Name the container `cron-nautilus`.

4. Utilize the `httpd` image with `latest tag` (specify as `httpd:latest`).

5. Execute the dummy command `echo Welcome to xfusioncorp!`.

6. Ensure the restart policy is `OnFailure`.

`Note:` The `kubectl` utility on `jump_host` is configured to operate with the Kubernetes cluster.

---

## Solution

1. **Create the YAML file**

Save this as `nautilus-cronjob.yaml`:

```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: nautilus
spec:
  schedule: "*/12 * * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: cron-nautilus
            image: httpd:latest
            command: ["/bin/sh", "-c", "echo Welcome to xfusioncorp!"]
          restartPolicy: OnFailure
```
---

2. **Apply the cronjob**

```bash
kubectl apply -f nautilus-cronjob.yaml
```
---

3. **Verify the cronjob**

Check if the cronjob is created:

```bash
kubectl get cronjobs
```
Check jobs created by it:

```bash
kubectl get jobs --watch
```

Check logs of the pod (replace with actual pod name):

```bash
kubectl logs <pod-name>
```
You should see:

```css
Welcome to xfusioncorp!
```
---

✅ This meets all requirements:

- CronJob named **nautilus**
- Schedule ***/12 * * * ***
- Container name **cron-nautilus**
- Image **httpd:latest**
- Command outputs ***Welcome to xfusioncorp!***
- Restart policy **OnFailure**