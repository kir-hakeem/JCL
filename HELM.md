
### **Fix:**
#### **1. Check Certificate Expiry**
Run:
```sh
kubectl get secret terraform-1edtech-net-tls -n default -o jsonpath="{.data.tls\.crt}" | base64 --decode | openssl x509 -noout -text | grep "Not After"
```
- If the date is in the past, your cert is expired.

#### **2. Recreate the Secret (if needed)**
If expired or corrupted, **delete and recreate it**:

```sh
kubectl delete secret terraform-1edtech-net-tls -n default
kubectl create secret tls terraform-1edtech-net-tls --cert=fullchain.pem --key=privkey.pem -n default
```
(Replace `fullchain.pem` and `privkey.pem` with your actual certificate and key files.)

#### **3. Restart Ingress**
```sh
kubectl rollout restart deployment ingress-nginx-controller -n ingress-nginx
```
