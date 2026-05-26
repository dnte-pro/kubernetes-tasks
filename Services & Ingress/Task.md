# Services & Ingress (Expose to outside)


## Scenario: Expose web service to https://ecostore.local for internal testing.

- Create NodePort service for web deployment (just to test external access).

- nstall ingress controller (e.g., nginx-ingress via Helm or minikube addons enable ingress).

- Create Ingress resource web-ingress with:

- Host: ecostore.local

- Path: / → service web-svc port 80

- Test using curl -H "Host: ecostore.local" <ingress-ip> (or add to /etc/hosts).


# Solution

#### **Prerequisites:**
The task does not mention the creation of namespace and deployment:
   - Deployment = web 
   - Namespace = dev
  
1. Create Nodeport Service for Web Deployment

 - Expose the existing deployment externally:

```bash 
kubectl expose deployment web \
  --name=web-nodeport \
  --type=NodePort \
  --port=80 \
  --target-port=80 \
  -n dev
```
 - Verify the service 

```bash 
kubectl get svc -n dev
```
 - Look for:
```bash
80:<nodeport>/TCP
```
 - Then access the service via the port:

```bash
 curl http://<NODE-IP>:<NODEPORT>
```

**I used the Killercoda labs and I used the INTERNAL-IP as the NODE-IP:**
```bash 
kubectl get nodes -o wide
```
![Expose](https://github.com/user-attachments/assets/874ec690-e8cd-4112-93d7-fff3951da8b0)


## 2. **Install NGINX Ingress Controller**
Since the killercoda labs already have helm installed, I opted to use helm rather than minikube


- Helm Repo addition:
```bash
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
```
- Update the repos:
```bash
helm repo udate
```

- Install controller 
```bash
helm install ingress-nginx ingress-nginx/ingress-nginx \
  --namespace ingress-nginx \
  --create-namespace
```

- Verify:
```bash 
kubectl get pods -n ingress-nginx
```
![Ingress Controller](https://github.com/user-attachments/assets/83037623-b6b3-4ecb-9b6d-e4107e22b502)



### 3. **Create Ingress Resource**

Create the web-ingress.yaml

```bash
vim web-ingress.yaml
```
Then paste the configuration:

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress

metadata:
  name: web-ingress
  namespace: dev

spec:
  ingressClassName: nginx

  rules:
  - host: ecostore.local

    http:
      paths:
      - path: /
        pathType: Prefix

        backend:
          service:
            name: web-nodeport
            port:
              number: 80
```

- Apply the web-ingress.yaml
```bash
kubectl apply -f web-ingress.yaml
```

- Verify the ingress:
```bash 
kubectl get ingress -n dev
```
![web-ingress](https://github.com/user-attachments/assets/7ec394b0-1e8e-4ce3-a761-5dc41ac67859)



### 4. **Get Ingress IP**
- To get the ExternalIP we get the nodes INTERNAL-IP

```bash
kubectl get svc -n ingress-nginx
```



### 5. Test using curl

Test if the Ingress is working:

```bash
curl -H "Host: ecostore.local" http://<INGRESS-IP>
```
NB: Change the <INGRESS-IP> with the IP address from the previous step

![Curl](https://github.com/user-attachments/assets/2880c2c5-f6ef-4238-a1e5-45f2c9800708)

### 6. Add Local DNS Entry

Edit the /etc/hosts
```bash
sudo vim /etc/hosts
```

Add:
```bash
<INGRESS-IP>   ecostore.local
```

Then test from the terminal:
```bash
curl http://ecostore.local
```
![Local DNS](https://github.com/user-attachments/assets/18d71778-9463-4dd9-9288-786fe935b4a7)
