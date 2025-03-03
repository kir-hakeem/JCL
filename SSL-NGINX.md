SSL CONFIG 
    
    
    server {
        listen 80;
        listen 8080;
        listen 443 ssl;
        server_name 198.199.68.144;  # Your server's IP address
    
        ssl_certificate /etc/letsencrypt/live/jenkins.1edtech.org/fullchain.pem;
        ssl_certificate_key /etc/letsencrypt/live/jenkins.1edtech.org/privkey.pem;
    
        location / {
            return 301 https://jenkins.1edtech.org$request_uri;
        }
    }
    
    # Main SSL server block for Jenkins
    server {
        listen 443 ssl;
        server_name jenkins.1edtech.org;
    
        ssl_certificate /etc/letsencrypt/live/jenkins.1edtech.org/fullchain.pem;
        ssl_certificate_key /etc/letsencrypt/live/jenkins.1edtech.org/privkey.pem;
    
        location / {
            proxy_pass http://127.0.0.1:8080;  # If Jenkins runs on 8080
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto https;
        }
    }
    
    # Redirect HTTP to HTTPS
    server {
        listen 80;
        listen 8080;
        server_name jenkins.1edtech.org;
    
        location / {
            return 301 https://jenkins.1edtech.org$request_uri;
        }
    }
