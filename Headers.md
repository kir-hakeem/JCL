### 🔐 Apache Security Headers (inside `<VirtualHost *:80>` or `*:443`)

```apache
# Prevent MIME type sniffing
Header always set X-Content-Type-Options "nosniff"

# Prevent clickjacking
Header always set X-Frame-Options "SAMEORIGIN"

# Basic Content Security Policy (adjust if needed)
Header always set Content-Security-Policy "default-src 'self'; script-src 'self'; style-src 'self';"

# Hide referrer on cross-origin requests
Header always set Referrer-Policy "strict-origin-when-cross-origin"

# Enforce HTTPS
Header always set Strict-Transport-Security "max-age=31536000; includeSubDomains; preload"
```

---

