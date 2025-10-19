# Task 11: Fix issue with LAMP Environment in Kubernetes

## Question

One of the DevOps team member was trying to install a WordPress website on a LAMP stack which is essentially deployed on Kubernetes cluster. It was working well and we could see the installation page a few hours ago. However something is messed up with the stack now due to a website went down. Please look into the issue and fix it:

**Task:**

FYI, deployment name is `lamp-wp` and its using a service named `lamp-service`. The Apache is using http default port and nodeport is `30008`. From the application logs it has been identified that application is facing some issues while connecting to the database in addition to other issues. Additionally, there are some environment variables associated with the pods like `MYSQL_ROOT_PASSWORD, MYSQL_DATABASE,  MYSQL_USER, MYSQL_PASSWORD, MYSQL_HOST`.


Also do not try to delete/modify any other existing components like deployment name, service name, types, labels etc.

`Note:` The `kubectl` utility on `jump_host` is configured to operate with the Kubernetes cluster.

```bash
kubectl edit svc lamp-service
```
The Result is :

```yaml
apiVersion: v1
kind: Service
metadata:
  name: lamp-service
spec:
  type: NodePort
  selector:
    app: lamp
  ports:
  - port: 80
    targetPort: 80
    nodePort: 30009
```

```bash
cat /app/index.php
```
The Result is :
```php
<?php
$dbname = $_ENV['MYSQL_DATABASE'];
$dbuser = $_ENV['MYSQL_USER'];
$dbpass = $_ENV['MYSQL_PASSWORDS']; 
$dbhost = $_ENV['MYSQL-HOST'];
?>
```

---

## SOLUTION

## 🧩 Fix the Service (NodePort)

The task states:

  Apache is using HTTP default port and nodePort is `30008`.

But your current service shows:
```yaml
nodePort: 30009
```
**✅ Fix:**
Edit the service to match the expected NodePort (30008):

```bash
kubectl edit svc lamp-service
```
Then modify this part:
```yaml
ports:
  - port: 80
    targetPort: 80
    nodePort: 30008
```
Save and exit.

To verify:
```bash
kubectl get svc lamp-service
```
You should see:
```cpp
lamp-service   NodePort   ...   80:30008/TCP
```

## 🧩 Fix the PHP database connection variables

Here’s the incorrect PHP snippet you showed:

```php
<?php
$dbname = $_ENV['MYSQL_DATABASE'];
$dbuser = $_ENV['MYSQL_USER'];
$dbpass = $_ENV['MYSQL_PASSWORDS']; 
$dbhost = $_ENV['MYSQL-HOST'];
?>
```
There are **multiple issues** here:

- Wrong variable names:
  - `MYSQL_PASSWORDS` ❌ should be `MYSQL_PASSWORD` ✅
  - `MYSQL-HOST` ❌ should be `MYSQL_HOST` ✅

✅ **Fix the file:**

```bash
vi /app/index.php
```
Replace the lines with:
```php
<?php
$dbname = $_ENV['MYSQL_DATABASE'];
$dbuser = $_ENV['MYSQL_USER'];
$dbpass = $_ENV['MYSQL_PASSWORD'];
$dbhost = $_ENV['MYSQL_HOST'];
?>
```
Save and exit.

## 🔄 Verify the fix

Restart the pod so it reloads environment variables:
```bash
kubectl delete pod -l app=lamp
```
Kubernetes will automatically recreate it using the same deployment.

Check status:
```bash
kubectl get pods -l app=lamp
```
Wait until it’s `Running`.

## 🌐 4. Test the application

Once pod is running, open the app using the node’s IP:
```ccp
http://<NodeIP>:30008
```
You should now see page again 🎉