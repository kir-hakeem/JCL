2. Install Required Dependencies

Run the following commands on each server to install the necessary dependencies:

    sudo apt update
    sudo apt install -y build-essential zlib1g-dev libssl-dev libpam0g-dev

3. Download and Extract OpenSSH 9.8p1

Navigate to the source directory and download the latest OpenSSH version:

    cd /usr/local/src
    sudo wget https://cdn.openbsd.org/pub/OpenBSD/OpenSSH/portable/openssh-9.8p1.tar.gz
    sudo tar -xvzf openssh-9.8p1.tar.gz
    cd openssh-9.8p1

4. Compile and Install OpenSSH 9.8p1

Compile and install OpenSSH manually:

    sudo ./configure --prefix=/usr --sysconfdir=/etc/ssh
    sudo make -j$(nproc)
    sudo make install

5. Verify the Updated Version

Once installation is complete, verify that OpenSSH has been successfully updated:

    ssh -V

6. Restart SSH Service (If Needed)

If necessary, restart the SSH service to apply changes:

