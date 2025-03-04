server {
    listen 80 default_server;
    listen 443 ssl default_server;
    listen 8080 default_server;
    
    server_name _;
    
    ssl_certificate /etc/letsencrypt/live/jenkins.ledtech.org/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/jenkins.ledtech.org/privkey.pem;

    return 403;  # Block direct access via IP on both HTTP and HTTPS
}
