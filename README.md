Option A: Fix YUM repositories (temporary solution)


            sudo sed -i 's/mirrorlist/#mirrorlist/g' /etc/yum.repos.d/CentOS-*
            sudo sed -i 's|#baseurl=http://mirror.centos.org|baseurl=http://vault.centos.org|g' /etc/yum.repos.d/CentOS-*
            sudo yum clean all
            sudo yum makecache

Option B: Better approach - skip Certbot for MySQL (recommended)
Since this is a MySQL server, we don't actually need Certbot. Let's create proper certificates manually.
2. Create SSL certificates for MySQL


# Create directory for certificates
            sudo mkdir /etc/mysql-ssl
            cd /etc/mysql-ssl

# Generate CA key and certificate
            sudo openssl genrsa -out ca-key.pem 2048
            sudo openssl req -new -x509 -nodes -days 3650 -key ca-key.pem -out ca.pem

# Generate server key and certificate
            sudo openssl req -newkey rsa:2048 -days 3650 -nodes -keyout server-key.pem -out server-req.pem
            sudo openssl rsa -in server-key.pem -out server-key.pem
            sudo openssl x509 -req -in server-req.pem -days 3650 -CA ca.pem -CAkey ca-key.pem -set_serial 01 -out server-cert.pem

# Set proper permissions
            sudo chmod 400 *.pem
            sudo chown mysql:mysql *.pem
