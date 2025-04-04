v=spf1 a ip4:198.2.128.0/18 ip4:148.105.0.0/16 ip4:205.201.128.0/20 include:cvent-planner.com include:_spf.google.com include:sendgrid.net include:amazonses.com include:spf.protection.outlook.com ~all


### **1. Generate a TLS Certificate for `app.1edtech.org`**
If you're using **cert-manager**, create a certificate request YAML:

```yaml
apiVersion: cert-manager.io/v1
kind: Certificate
metadata:
  name: app-1edtech-cert
  namespace: default
spec:
  secretName: app-1edtech-net-tls
  issuerRef:
    name: cluster-issuer
    kind: ClusterIssuer
  dnsNames:
    - app.1edtech.org
```

Apply this configuration:

```sh
kubectl apply -f cert-app-1edtech.yaml
```

---

### **2. Verify the Certificate**
Check if the certificate has been issued:

```sh
kubectl get certificate app-1edtech-cert -n default
```

Check the status:

```sh
kubectl describe certificate app-1edtech-cert -n default
```

If successful, you should see `Ready: True`.

---

### **3. Ensure Ingress Uses the New Secret**
Now, update your Ingress to use the new secret:

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: app-1edtech-ingress
  namespace: default
spec:
  tls:
  - hosts:
    - app.1edtech.org
    secretName: app-1edtech-net-tls
  rules:
  - host: app.1edtech.org
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: your-app-service
            port:
              number: 443
```

Apply the Ingress configuration:

```sh
kubectl apply -f ingress-app-1edtech.yaml
```

---

### **4. Restart Ingress Controller**
```sh
kubectl rollout restart deployment ingress-nginx-controller -n ingress-nginx
```

---

### **5. Test HTTPS Access**
Try accessing **`https://app.1edtech.org`** in your browser. If you still get SSL errors:
- Check logs:  
  ```sh
  kubectl logs -l app.kubernetes.io/name=ingress-nginx -n ingress-nginx
  ```
- Describe the certificate:
  ```sh
  kubectl describe certificate app-1edtech-cert -n default
  ```
