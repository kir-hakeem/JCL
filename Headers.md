### ✅ **1. GKE (NGINX Ingress)**
You’ve got this one mostly handled.

🔧 Add this annotation to each Ingress:

```yaml
nginx.ingress.kubernetes.io/configuration-snippet: |
  more_set_headers "Referrer-Policy: strict-origin-when-cross-origin";
  more_set_headers "X-Frame-Options: SAMEORIGIN";
  more_set_headers "X-Content-Type-Options: nosniff";
  more_set_headers "X-XSS-Protection: 1; mode=block";
  more_set_headers "Strict-Transport-Security: max-age=63072000; includeSubDomains; preload";
```

🧪 Then test:
```bash
curl -I https://<gke-subdomain>
```

---

### ✅ **2. NGINX on Compute Engine (VMs)**

🔧 Edit your NGINX config (usually in `/etc/nginx/sites-enabled/default` or similar):

Inside the `server` block, add:

```nginx
add_header Referrer-Policy "strict-origin-when-cross-origin";
add_header X-Frame-Options "SAMEORIGIN";
add_header X-Content-Type-Options "nosniff";
add_header X-XSS-Protection "1; mode=block";
add_header Strict-Transport-Security "max-age=63072000; includeSubDomains; preload";
```

> 📌 If you're proxying to upstreams, add `always`:
```nginx
add_header Referrer-Policy "strict-origin-when-cross-origin" always;
```

🔄 Then:
```bash
sudo nginx -t
sudo systemctl reload nginx
```

---

### ✅ **3. Apache on Compute Engine (VMs)**

🔧 Edit your Apache virtual host file, e.g. `/etc/apache2/sites-enabled/<your-site>.conf`

Inside `<VirtualHost *:443>` or `<VirtualHost *:80>`, add:

```apache
<IfModule mod_headers.c>
  Header always set Referrer-Policy "strict-origin-when-cross-origin"
  Header always set X-Frame-Options "SAMEORIGIN"
  Header always set X-Content-Type-Options "nosniff"
  Header always set X-XSS-Protection "1; mode=block"
  Header always set Strict-Transport-Security "max-age=63072000; includeSubDomains; preload"
</IfModule>
```

🧪 Then reload:
```bash
sudo apachectl configtest
sudo systemctl reload apache2
```

---

