SSL CONFIG 
    
    server {
        listen 80;
        listen 8080;
        listen 443 ssl;
        server_name 198.199.68.144;  # Replace with your actual IP
    
        ssl_certificate /etc/letsencrypt/live/jenkins.1edtech.org/fullchain.pem;
        ssl_certificate_key /etc/letsencrypt/live/jenkins.1edtech.org/privkey.pem;
    
        location / {
            return 301 https://jenkins.1edtech.org$request_uri;
        }
    }
    
    server {
        listen 80;
        listen 8080;
        server_name _;
    
        location / {
            return 301 https://jenkins.1edtech.org$request_uri;
        }
    }
