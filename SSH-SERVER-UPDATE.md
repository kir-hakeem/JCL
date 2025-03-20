Upgrading OpenSSH to Version 9.8p1

Summary

Due to the unavailability of an updated OpenSSH package via the default package manager, a manual upgrade to OpenSSH 9.8p1 was performed. This process involved installing necessary dependencies, downloading the latest OpenSSH source code, compiling it, and verifying the installation.
The upgrade was successfully applied to the server ansible.1edtech.org (IP: 143.198.165.9). However, the servers casenetwork.1edtech.org, prod.1edtech.org, and learn.1edtech.org (IPs: 18.220.1.216, 34.170.58.136, and 34.70.4.11 respectively) could not be found, and therefore, OpenSSH was not updated on those machines.
Steps for Manual Upgrade
1. Install Required Dependencies
Before manually compiling OpenSSH, install essential dependencies:

    sudo apt update
    sudo apt install -y build-essential zlib1g-dev libssl-dev libpam0g-dev


2. Download and Extract OpenSSH 9.8p1
The latest portable OpenSSH version is downloaded and extracted manually:

    cd /usr/local/src
    sudo wget https://cdn.openbsd.org/pub/OpenBSD/OpenSSH/portable/openssh-9.8p1.tar.gz
    sudo tar -xvzf openssh-9.8p1.tar.gz
    cd openssh-9.8p1


3. Compile and Install OpenSSH 9.8p1
To manually compile and install OpenSSH:


    sudo ./configure --prefix=/usr --sysconfdir=/etc/ssh
    sudo make -j$(nproc)
    sudo make install
    
4. Verify the Updated Version
After installation, check the updated OpenSSH version:
ssh -V
