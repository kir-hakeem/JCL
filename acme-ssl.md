Install acme.sh First, install acme.sh if it's not already installed:

    curl https://get.acme.sh | sh


After the installation, restart your shell session or run:

    source ~/.bashrc


Issue the SSL Certificate 

    sudo systemctl stop httpd
    ~/.acme.sh/acme.sh --issue --standalone -d onerostervalidator.imsglobal.org
    sudo systemctl start httpd


Install the SSL Certificate 

    ~/.acme.sh/acme.sh --install-cert -d onerostervalidator.imsglobal.org \
    --key-file /etc/ssl/private/onerostervalidator.key \
    --fullchain-file /etc/ssl/certs/onerostervalidator.crt


Configure Your Server to Use the SSL Certificate 
for Apache

    SSLCertificateFile /etc/ssl/certs/onerostervalidator.crt
    SSLCertificateKeyFile /etc/ssl/private/onerostervalidator.key

for Nginx

    ssl_certificate /etc/ssl/certs/onerostervalidator.crt;
    ssl_certificate_key /etc/ssl/private/onerostervalidator.key;

Automate SSL Renewal 

    crontab -e

Add the following line to renew the certificate daily:

    0 0 * * * ~/.acme.sh/acme.sh --cron --home ~/.acme.sh












