SSL CONFIG 
    
    server {
        listen 80 default_server;
        listen [::]:80 default_server;
        server_name _;
    
        return 444;  # Drop connection for direct IP access
    }
    
    server {
        listen 443 ssl default_server;
        listen [::]:443 ssl default_server;
        server_name _;
    
        ssl_certificate /etc/letsencrypt/live/jenkins.ledtech.org/fullchain.pem;
        ssl_certificate_key /etc/letsencrypt/live/jenkins.ledtech.org/privkey.pem;
    
        return 444;  # Drop connection for direct IP access
    }
    
    server {
        listen 80;
        listen [::]:80;
        server_name jenkins.ledtech.org;
    
        return 301 https://$host$request_uri;
    }
    
    server {
        listen 443 ssl;
        listen [::]:443 ssl;
        server_name jenkins.ledtech.org;
    
        ssl_certificate /etc/letsencrypt/live/jenkins.ledtech.org/fullchain.pem;
        ssl_certificate_key /etc/letsencrypt/live/jenkins.ledtech.org/privkey.pem;
    
        location / {
            proxy_pass http://localhost:8080;  # Update based on your Jenkins setup
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        }
    }
