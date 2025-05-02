#### Step 1: Add the PPA manually

```bash
sudo apt install -y software-properties-common ca-certificates lsb-release apt-transport-https
sudo mkdir -p /etc/apt/keyrings
sudo wget -qO /etc/apt/keyrings/ondrej-php.gpg https://packages.sury.org/php/apt.gpg
echo "deb [signed-by=/etc/apt/keyrings/ondrej-php.gpg] https://packages.sury.org/php $(lsb_release -sc) main" | sudo tee /etc/apt/sources.list.d/php.list
```

#### Step 2: Update and install PHP 8.2

```bash
sudo apt update
sudo apt install php8.2 php8.2-cli php8.2-common php8.2-fpm php8.2-mysql php8.2-xml php8.2-curl php8.2-zip php8.2-mbstring php8.2-gd
```

#### Step 3: Enable PHP 8.2 for Apache

```bash
sudo a2dismod php8.1
sudo a2enmod php8.2
sudo systemctl restart apache2
```

