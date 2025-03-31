## **1. Create ClusterIssuer (`letsencrypt-staging`)**
Save this as `clusterissuer.yaml`:

```yaml
apiVersion: cert-manager.io/v1
kind: ClusterIssuer
metadata:
  name: letsencrypt-staging
  namespace: cert-manager
spec:
  acme:
    email: pnicholls@imaglobal.org
    server: https://acme-staging-v02.api.letsencrypt.org/directory
    privateKeySecretRef:
      name: terraform-staging-key
    solvers:
      - http01:
          ingress:
            class: nginx
```

Apply it:
```sh
kubectl apply -f clusterissuer.yaml
```

---

## **2. Create Certificate (`staging-1edtech-cert`)**
Save this as `certificate.yaml`:

```yaml
apiVersion: cert-manager.io/v1
kind: Certificate
metadata:
  name: staging-1edtech-cert
  namespace: default
spec:
  secretName: staging-1edtech-net-tla
  issuerRef:
    name: letsencrypt-staging
    kind: ClusterIssuer
  commonName: staging.1edtech.org
  dnsNames:
    - staging.1edtech.org
  privateKey:
    algorithm: RSA
    size: 2048
  usages:
    - digital signature
    - key encipherment
```

Apply it:
```sh
kubectl apply -f certificate.yaml
```

---

## **3. Create Ingress (if needed)**
Save this as `ingress.yaml`:

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: staging-1edtech-ingress
  namespace: default
  annotations:
    cert-manager.io/cluster-issuer: letsencrypt-staging
spec:
  ingressClassName: nginx
  rules:
  - host: staging.1edtech.org
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: my-service
            port:
              number: 80
  tls:
  - hosts:
    - staging.1edtech.org
    secretName: staging-1edtech-net-tla
```

Apply it:
```sh
kubectl apply -f ingress.yaml
```

---

## **4. Verify Everything is Working**
Run the following checks:

```sh
kubectl get clusterissuer letsencrypt-staging -o yaml
kubectl get certificate staging-1edtech-cert -o yaml
kubectl describe certificate staging-1edtech-cert
kubectl get secret staging-1edtech-net-tla
kubectl logs -n cert-manager deploy/cert-manager | grep staging-1edtech
```

---

### **Next Steps**
- **Check if HTTPS is working:**  
  ```sh
  curl -v https://staging.1edtech.org
  ```
- **If the certificate is stuck in `Pending`:**  
  ```sh
  kubectl get events --sort-by=.metadata.creationTimestamp
  ```
- **If you see errors, check cert-manager logs:**  
  ```sh
  kubectl logs -n cert-manager deploy/cert-manager
  ```
