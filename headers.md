

### For NGINX:

```nginx
server {
    # ... existing configuration ...

    # Security Headers
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-XSS-Protection "1; mode=block" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header Referrer-Policy "strict-origin-when-cross-origin" always;
    add_header Content-Security-Policy "default-src 'self'; frame-ancestors 'self';" always;
    add_header Strict-Transport-Security "max-age=63072000; includeSubDomains; preload" always;
    
    # ... rest of configuration ...
}
```

### For Apache:

```apache
<VirtualHost *:80>
    # ... existing configuration ...

    # Security Headers
    Header always set X-Frame-Options "SAMEORIGIN"
    Header always set X-XSS-Protection "1; mode=block"
    Header always set X-Content-Type-Options "nosniff"
    Header always set Referrer-Policy "strict-origin-when-cross-origin"
    Header always set Content-Security-Policy "default-src 'self'; frame-ancestors 'self';"
    Header always set Strict-Transport-Security "max-age=63072000; includeSubDomains; preload"
    
    # ... rest of configuration ...
</VirtualHost>
```


## Implementation Steps:

### NGINX:
1. Edit your configuration file:
   ```bash
   sudo nano /etc/nginx/sites-available/your-site
   ```
2. Add all headers shown above in the server block
3. Test configuration:
   ```bash
   sudo nginx -t
   ```
4. Reload NGINX:
   ```bash
   sudo systemctl reload nginx
   ```

### Apache:
1. Edit your configuration file:
   ```bash
   sudo nano /etc/apache2/sites-available/your-site.conf
   ```
2. Add all headers shown above in the VirtualHost block
3. Enable headers module if not already enabled:
   ```bash
   sudo a2enmod headers
   ```
4. Test configuration:
   ```bash
   sudo apache2ctl configtest
   ```
5. Restart Apache:
   ```bash
   sudo systemctl restart apache2
   ```
