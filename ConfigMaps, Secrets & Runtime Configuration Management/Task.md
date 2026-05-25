# Task 2: ConfigMaps & Secrets (Real app config)
Scenario: The api pod needs non-sensitive config (e.g., log level) and a database password secret.

Create ConfigMap api-config with:

LOG_LEVEL=debug

API_TIMEOUT=30s

Create Secret db-secret (Opaque) with:

DB_PASSWORD=SuperSecret123 (encode properly or use literal)

Create Deployment api (1 replica, image busybox with sleep infinity) that mounts:

ConfigMap as env vars

Secret as env var

exec into pod and verify env vars are present.

## Solution 

#### 1. Create the ConfigMap
Create the configmap with the given specifications:
- LOG_LEVEL=debug 
- API_TIMEOUT=30s

```bash
kubectl create configmap api-config \
  --from-literal=LOG_LEVEL=debug \
  --from-literal=API_TIMEOUT=30s \
  -n development
```


**Ensure that there is a namespace called development, otherwise create it:**
```bash
kubectl create namespace development
```

Verify the configmap:
```bash
kubectl get configmap api-config -n development -o yaml
```



#### 2. Create the Secret
Create the Secret "db_secret" with the given specification:
- DB_PASSWORD=SuperSecret123 (encode properly or use literal)

```bash
kubectl create secret generic db_secret \
  --from-literal=DB_PASSWORD=SuperSecret123 \
  -n development
```

Verify the Secret:  
```bash 
kubectl get secret db_secret -n development -o yaml
```
The password has been encoded to base64


#### 3. Create the Deployment having the Configmap and the Secret:

 create api.yaml and then open the file ```vim api.yaml```
 Paste the below to the file:
 ```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  name: api
  namespace: development

spec:
  replicas: 1

  selector:
    matchLabels:
      app: api

  template:
    metadata:
      labels:
        app: api

    spec:
      containers:
      - name: api
        image: busybox

        command:
        - sh
        - -c
        - sleep infinity

        envFrom:
        - configMapRef:
            name: api-config

        - secretRef:
            name: db-secret
 ```



 - Apply the deployment:
```bash
 kubectl apply -f api.yaml
 ```



#### 4.  Verify the deployment:
 ```bash
 kubectl get pods -n deployment 
 ```

 Ensure the pod is Running before proceeding.




#### 5. Exec into the pod
Get the pod name:
```bash
kubectl get pod -n development
```

Exec into the pod:
```bash
kubectl exec -it <pod name> -n development -- sh
```


#### 6. Verify the Variables
- check log level :
```bash 
echo $LOG_LEVEL
```
Output = ```debug```

- check timeout:
```bash
echo $API_TIMEOUT
```
output=```30s```

- check secret:
```bash
echo $DB_PASSWORD
```
output=```SuperSecret123```
- exit the container 
```bash
exit
```