Install the CentOS 7 Repository:
Download the CentOS 7 repository configuration:
bash
Copy

    curl -o /etc/yum.repos.d/CentOS-Base.repo https://vault.centos.org/7.9.2009/os/x86_64/.treeinfo

Upgrade the OS:
Run the following commands to upgrade to CentOS 7:
bash
Copy

    yum clean all
    yum update
    yum upgrade

Reboot the Droplet:
bash
Copy

    reboot

Verify the Upgrade:

    rpm -Uvh https://vault.centos.org/7.9.2009/os/x86_64/Packages/centos-release-7-9.2009.1.el7.centos.x86_64.rpm
