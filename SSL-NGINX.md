ssl config


    server {
        listen 80;
        server_name jenkins.1edtech.org;
    
        # Redirect HTTP to HTTPS
        return 301 https://$host$request_uri;
    }
    
    server {
        listen 443 ssl;
        server_name jenkins.1edtech.org;
    
        # SSL configuration (use Certbot-generated certificates)
        ssl_certificate /etc/letsencrypt/live/jenkins.1edtech.org/fullchain.pem;
        ssl_certificate_key /etc/letsencrypt/live/jenkins.1edtech.org/privkey.pem;
    
        # Restrict access to only the domain
        if ($host != "jenkins.1edtech.org") {
            return 403;
        }
    
        # Proxy to Jenkins
        location / {
            proxy_pass http://localhost:8080;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }
    }
