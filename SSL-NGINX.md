SSL CONFIG

    server {
        listen 80;
        server_name 198.199.68.144;
        return 301 https://jenkins.ledtech.org$request_uri;
    }
    
    server {
        listen 443 ssl;
        server_name 198.199.68.144;
    
        ssl_certificate /etc/letsencrypt/live/jenkins.ledtech.org/fullchain.pem;
        ssl_certificate_key /etc/letsencrypt/live/jenkins.ledtech.org/privkey.pem;
    
        return 301 https://jenkins.ledtech.org$request_uri;
    }
