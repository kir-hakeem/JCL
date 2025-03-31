# **Final Fix for HTTP → HTTPS Redirect in Moodle Apache Config**

Your current `moodle.conf` has a **syntax error** in the rewrite rule that's preventing proper HTTPS redirection. Here's how to fix it:

## **1. Correct the RewriteRule Syntax**
Edit your Moodle config:
```bash
sudo nano /etc/apache2/sites-enabled/moodle.conf
```

Replace the entire file with:
```apache
<VirtualHost *:80>
    ServerName prod.1edtech.org
    DocumentRoot /var/www/moodle-1click
    
    # Force HTTPS redirect (corrected syntax)
    RewriteEngine On
    RewriteCond %{HTTPS} off
    RewriteRule ^ https://%{HTTP_HOST}%{REQUEST_URI} [L,R=301]

    <Directory /var/www/moodle-1click>
        Options -Indexes
        AllowOverride None
        Require all granted
    </Directory>

    <DirectoryMatch "/\.git">
        Require all denied
    </DirectoryMatch>

    ErrorLog ${APACHE_LOG_DIR}/moodle-error.log
    CustomLog ${APACHE_LOG_DIR}/moodle-access.log combined
</VirtualHost>
```

### Key Fixes:
1. **Fixed RewriteRule** (removed malformed `%[SERVER_NAME]` and `]`)
2. **Simplified redirect logic** (now checks `%{HTTPS} off` instead of server name)
3. **Proper 301 redirect** (`R=301` for permanent moves)

---

## **2. Test & Apply Changes**
```bash
# Check for syntax errors
sudo apache2ctl configtest

# Restart Apache
sudo systemctl restart apache2
```

---

## **3. Verify the Fix**
### **Test 1: Command Line**
```bash
curl -I http://prod.ledtech.org
```
✅ **Should return:**
```
HTTP/1.1 301 Moved Permanently
Location: https://prod.ledtech.org/
```

### **Test 2: Browser Check**
1. Open `http://prod.ledtech.org` in Chrome/Firefox  
2. Should **automatically redirect** to `https://prod.ledtech.org`  
3. Check for **padlock icon** in the address bar  

---

## **4. (Optional) Harden Security Further**
Add to your SSL config (`/etc/apache2/sites-enabled/moodle-le-ssl.conf`):
```apache
# Enable HSTS (force browsers to use HTTPS)
Header always set Strict-Transport-Security "max-age=63072000; includeSubDomains; preload"

# Disable weak protocols
SSLProtocol all -SSLv2 -SSLv3 -TLSv1 -TLSv1.1
SSLCipherSuite EECDH+AESGCM:EDH+AESGCM:AES256+EECDH:AES256+EDH
```

Restart Apache again:
```bash
sudo systemctl restart apache2
```

---

## **Expected Outcome**
- All HTTP traffic **permanently redirects to HTTPS**  
- No more "HTTP without SSL/TLS" warnings  
- A+ SSL rating (test at [SSL Labs](https://www.ssllabs.com/ssltest/))  

Need adjustments? Share your test results! 🚀
