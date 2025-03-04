SSL CONFIG
    
    server {
        listen 80 default_server;
        listen 443 ssl default_server;
        
        server_name _;
        
        return 403;  # Reject requests coming via IP
    }
    
    server {
        listen 80;
        server_name jenkins.ledtech.org;
    
        return 301 https://$host$request_uri;  # Redirect HTTP to HTTPS
    }
    
    server {
        listen 443 ssl;
        server_name jenkins.ledtech.org;
    
        ssl_certificate /etc/letsencrypt/live/jenkins.ledtech.org/fullchain.pem;
        ssl_certificate_key /etc/letsencrypt/live/jenkins.ledtech.org/privkey.pem;
    
        location / {
            proxy_pass http://localhost:8080;  # Forward to Jenkins
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }
    }
