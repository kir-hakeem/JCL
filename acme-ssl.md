Ensure acme.sh is Installed Correctly:

Make sure acme.sh is installed and working correctly. If not, reinstall it using the following:

    curl https://get.acme.sh | sh

Issue a New Certificate:

    _acme-challenge.onerostervalidator.imsglobal.org -> <value provided by acme.sh>


Use acme.sh to issue a new certificate for your domain. This will automatically generate the required certificate and private key files. Run the following command:

    ~/.acme.sh/acme.sh --issue --dns dns_squarespace -d onerostervalidator.imsglobal.org


Ensure /var/www/html is the correct webroot where the challenge files can be placed for validation. If you're unsure, check your web server configuration for the correct path.

Install the New Certificate:

Once the certificate is successfully issued, install it with:

    ~/.acme.sh/acme.sh --install-cert -d onerostervalidator.imsglobal.org \
    --key-file /etc/ssl/private/onerostervalidator.key \
    --fullchain-file /etc/ssl/certs/onerostervalidator.crt

This will install the new certificate and key into the specified paths.

Update the Apache Configuration (if needed):

Ensure the ssl.conf points to the correct files:

    SSLCertificateFile /etc/ssl/certs/onerostervalidator.crt
    SSLCertificateKeyFile /etc/ssl/private/onerostervalidator.key

Restart Apache:

Restart Apache to apply the new certificate:

    sudo service httpd restart

Verify the Certificate:

Finally, check if the new certificate is correctly applied by visiting your domain using HTTPS or using tools like openssl or an online SSL checker.

Example command:

    openssl s_client -connect onerostervalidator.imsglobal.org:443

