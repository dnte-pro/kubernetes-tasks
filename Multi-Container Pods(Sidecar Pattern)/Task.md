# Task: Multi-Container Pods (Sidecar Pattern)
## Scenario: 
web pod needs a logging sidecar that tails access logs.

Create Pod web-sidecar with:

Main container nginx

Sidecar container busybox that runs tail -f /var/log/nginx/access.log

Shared emptyDir volume mounted at /var/log/nginx in both containers.

kubectl logs web-sidecar -c sidecar to see logs.


## Solution:

1. Create the multi-container pod
- Create web-sidecar.yaml
```bash 
vim web-sidecar.yaml
```

- Paste the configurations:
```yaml
apiVersion: v1
kind: Pod

metadata:
  name: web-sidecar
  namespace: dev

spec:
  containers:

  # Main application container
  - name: nginx
    image: nginx

    ports:
    - containerPort: 80

    volumeMounts:
    - name: shared-logs
      mountPath: /var/log/nginx

  # Logging sidecar container
  - name: sidecar
    image: busybox

    command:
    - sh
    - -c
    - tail -f /var/log/nginx/access.log

    volumeMounts:
    - name: shared-logs
      mountPath: /var/log/nginx

  volumes:
  - name: shared-logs
    emptyDir: {}
```

- Apply it:

```bash
kubectl apply -f web-sidecar.yaml
```

![web-sidecar](https://github.com/user-attachments/assets/09763b66-69cb-4c55-8712-0272780367c6)



2. Verify the pod

To verify the pod was created, run:
```bash
kubectl get pods -n dev
```


![web-sidecar](https://github.com/user-attachments/assets/09763b66-69cb-4c55-8712-0272780367c6)


3. Generate NGINX Access logs

- Port-forward the pod port
```bash
kubectl port-forward pod/web-sidecar 8080:80 -n dev
```
![port-forward](https://github.com/user-attachments/assets/000bf6ee-1b27-41ce-8288-8f8817b87da9")

- Open anotheer terminal to use it to generate traffic and run multiple rimes to see effect

```bash
curl http://localhost:8080
```

![curl](https://github.com/user-attachments/assets/e9061bd9-ceba-4611-98e0-23f094dd4f3d)

4. View logs from sidecar container
- Check the sidecar logs:

```bash
kubectl logs web-sidecar \
  -c sidecar \
  -n dev
```

Expected output
```text
127.0.0.1 - - [27/May/2026:21:50:48 +0000] "GET / HTTP/1.1" 200 896 "-" "curl/7.81.0" "-"
```

![logs](https://github.com/user-attachments/assets/417036e3-45bd-4dc5-b6fb-4301ae61e868)

5. Verify shared Volume

Inspect the pod:
```bash
kubectl describe pod web-sidecar -n dev
```

![Volumes](https://github.com/user-attachments/assets/64f902ea-8c1f-42de-90cf-f239b20528cd)


