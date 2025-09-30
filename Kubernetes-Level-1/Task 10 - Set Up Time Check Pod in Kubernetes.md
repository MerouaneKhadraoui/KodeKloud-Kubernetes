# Task 10: Set Up Time Check Pod in Kubernetes

## Question

The Nautilus DevOps team needs a time check pod created in a specific Kubernetes namespace for logging purposes. Initially, it's for testing, but it may be integrated into an existing cluster later. Here's what's required:

**Task:**

1. Create a pod called `time-check` in the `devops` namespace. The pod should contain a container named `time-check`, utilizing the `busybox` image with the latest tag (specify as `busybox:latest`).

2. Create a config map named `time-config` with the data `TIME_FREQ=3` in the same namespace.

3. Configure the `time-check` container to execute the command: `while true; do date; sleep $TIME_FREQ;done`. Ensure the result is written `/opt/itadmin/time/time-check.log`. Also, add an environmental variable `TIME_FREQ` in the container, fetching its value from the config map `TIME_FREQ` key.

4. Create a volume `log-volume` and mount it at `/opt/itadmin/time` within the container.

`Note:` The `kubectl` utility on `jump_host` is configured to operate with the Kubernetes cluster.

---

## Solution

1. **Create the YAML file**

Save this as `all-in-one.yaml`:

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: devops
---
apiVersion: v1
kind: ConfigMap
metadata:
  name: time-config
  namespace: devops
data:
  TIME_FREQ: "3"
---
apiVersion: v1
kind: Pod
metadata:
  name: time-check
  namespace: devops
spec:
  containers:
    - name: time-check
      image: busybox:latest
      command: ["/bin/sh", "-c"]
      args:
        - while true; do date >> /opt/itadmin/time/time-check.log; sleep $TIME_FREQ; done
      env:
        - name: TIME_FREQ
          valueFrom:
            configMapKeyRef:
              name: time-config
              key: TIME_FREQ
      volumeMounts:
        - name: log-volume
          mountPath: /opt/itadmin/time
  volumes:
    - name: log-volume
      emptyDir: {}
```
---

2. **Apply it in one command**

```bash
kubectl apply -f all-in-one.yaml
```
---

3. **Verify**

- Check if pod is running:

```bash
kubectl get pods -n devops
```

- Exec into the pod and verify logs:

```bash
kubectl exec -it time-check -n devops -- tail -f /opt/itadmin/time/time-check.log
```

---

✅ With this, you have:

- Namespace `devops`
- ConfigMap `time-config` with `TIME_FREQ=3`
- Pod `time-check` with container `time-check`
- Log written into `/opt/itadmin/time/time-check.log`
- Volume mounted at `/opt/itadmin/time`