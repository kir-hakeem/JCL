    [mysqld]
    # Your existing settings
    bind-address = 127.0.0.1
    datadir=/var/lib/mysql
    socket=/var/lib/mysql/mysql.sock
    
    # Add these new SSL configurations (after your existing settings)
    ssl-ca=/etc/mysql-ssl/ca.pem
    ssl-cert=/etc/mysql-ssl/server-cert.pem
    ssl-key=/etc/mysql-ssl/server-key.pem
    require_secure_transport=ON  # This enforces SSL for all connections


CHECK

    mysql -u root -p -e "SHOW VARIABLES LIKE '%ssl%';"
