# Install Ubuntu 20.04.3 LTS

## Hardware requirements ##
The hardware requirements for installing a Ubuntu server with this manual only include a storage device for the Operating System.
You can use manual <HAVE YET TO WRITE THE MANUAL> for adding storage devices for data to the server.

Device    | Setting
--------- | -------
CPU Cores | 1
Memory    | 1 GB
Storage   | 60 GB

[Ubuntu 20.04.3 LTS Server Image](https://releases.ubuntu.com/20.04/ubuntu-20.04.4-live-server-amd64.iso)

These are the minimum requirements for setting up a Ubuntu server. Please ensure that your server has enough resources to
satisfy the services that are running on it.

As a base line for CPU/Memory you can use the ratio 1:2. For each CPU core assign 2 GB of memory.

## Basic setup
Ensure that the ISO image for Ubuntu 20.04.3 LTS is mounted to your Virtual Machine before starting it for the first time.

Startup the Virtual Machine and wait until the installer has started.

Choose English as the language for the installation.

![Select language](https://github.com/Tendermint-Validators/manuals/blob/main/images/Install_Ubuntu_20.04.3-LTS/setup_01.png?raw=true)

Continue without updating the installer.

![Select language](https://github.com/Tendermint-Validators/manuals/blob/main/images/Install_Ubuntu_20.04.3-LTS/setup_02.png?raw=true)

Use the default keyboard layout.

![Select language](https://github.com/Tendermint-Validators/manuals/blob/main/images/Install_Ubuntu_20.04.3-LTS/setup_03.png?raw=true)

Configure the network adapters for the server.
![Select language](https://github.com/Tendermint-Validators/manuals/blob/main/images/Install_Ubuntu_20.04.3-LTS/setup_04.png?raw=true)
![Select language](https://github.com/Tendermint-Validators/manuals/blob/main/images/Install_Ubuntu_20.04.3-LTS/setup_05.png?raw=true)

Confirm the address used as main Software repository.

![Select language](https://github.com/Tendermint-Validators/manuals/blob/main/images/Install_Ubuntu_20.04.3-LTS/setup_06.png?raw=true)

Select the entire disk.

![Select language](https://github.com/Tendermint-Validators/manuals/blob/main/images/Install_Ubuntu_20.04.3-LTS/setup_07.png?raw=true)

By default the Ubuntu installer proposes a bad partition layout. We have to clean this up first before we continue with the installation.

![Select language](https://github.com/Tendermint-Validators/manuals/blob/main/images/Install_Ubuntu_20.04.3-LTS/setup_08.png?raw=true)

Remove the device *ubuntu-lv* and rename the LVM volume group from *ubuntu-vg* to *vg_os*.

![Select language](https://github.com/Tendermint-Validators/manuals/blob/main/images/Install_Ubuntu_20.04.3-LTS/setup_09.png?raw=true)
![Select language](https://github.com/Tendermint-Validators/manuals/blob/main/images/Install_Ubuntu_20.04.3-LTS/setup_10.png?raw=true)

Create a new partition LVM layout

Volume        | Size   | Mount Point    | Filesystem
------------- | ------ | -------------- | ----------
root          | 20G    | /              | ext4
var           | 20G    | /var           | ext4
var_log       | 8G     | /var/log       | ext4
var_log_audit | 2G     | /var/log/audit | ext4
home          | 8.996G | /home          | ext4

![Select language](https://github.com/Tendermint-Validators/manuals/blob/main/images/Install_Ubuntu_20.04.3-LTS/setup_11.png?raw=true)

Confirm the new partition layour and continue with the installation.

![Select language](https://github.com/Tendermint-Validators/manuals/blob/main/images/Install_Ubuntu_20.04.3-LTS/setup_12.png?raw=true)

We need a default administrative account with a regular name. We only use this account for installing the Operating System.
The account will be disabled by Ansible when it configures the server for us.

Key         | Value
----------- | ----------------------
name        | Administrator
server name | <environment><role><id>
username    | administrator
password    | BatteryHorseStaple123!

![Select language](https://github.com/Tendermint-Validators/manuals/blob/main/images/Install_Ubuntu_20.04.3-LTS/setup_13.png?raw=true)

Select *Install OpenSSH server*.

![Select language](https://github.com/Tendermint-Validators/manuals/blob/main/images/Install_Ubuntu_20.04.3-LTS/setup_14.png?raw=true)

Do not select any additional packages. Just press **Done**.

![Select language](https://github.com/Tendermint-Validators/manuals/blob/main/images/Install_Ubuntu_20.04.3-LTS/setup_15.png?raw=true)

The installer will now install the Operating System.

![Select language](https://github.com/Tendermint-Validators/manuals/blob/main/images/Install_Ubuntu_20.04.3-LTS/setup_16.png?raw=true)

After the installer is done press *Reboot Now*

![Select language](https://github.com/Tendermint-Validators/manuals/blob/main/images/Install_Ubuntu_20.04.3-LTS/setup_17.png?raw=true)

Press *ENTER* to continue rebooting the server.

![Select language](https://github.com/Tendermint-Validators/manuals/blob/main/images/Install_Ubuntu_20.04.3-LTS/setup_18.png?raw=true)

The Operating System is now installed and we can access the server via SSH to perform the initial configuration.

![Select language](https://github.com/Tendermint-Validators/manuals/blob/main/images/Install_Ubuntu_20.04.3-LTS/setup_19.png?raw=true)

# Initial Configuration #

Open your favorite Terminal Emulator and connect to the server over SSH.
For Linux it is adviced to use **Terminator** (apt install Terminator)
For Windows it is adviced to use [PuTTY](https://the.earth.li/~sgtatham/putty/latest/w64/putty.exe) and [PuTTYGen](https://the.earth.li/~sgtatham/putty/latest/w64/puttygen.exe).

For conveniance we will use PuTTY in this manual.

## Start PuTTY ##

Open PuTTY and type in the address of the new Ubuntu server.

![Select language](https://github.com/Tendermint-Validators/manuals/blob/main/images/Install_Ubuntu_20.04.3-LTS/setup_20.png?raw=true)

Confirm the host key.

![Select language](https://github.com/Tendermint-Validators/manuals/blob/main/images/Install_Ubuntu_20.04.3-LTS/setup_21.png?raw=true)

Log in with the `administrator` account.

![Select language](https://github.com/Tendermint-Validators/manuals/blob/main/images/Install_Ubuntu_20.04.3-LTS/setup_22.png?raw=true)

Type `sudo su` to become user root. 

![Select language](https://github.com/Tendermint-Validators/manuals/blob/main/images/Install_Ubuntu_20.04.3-LTS/setup_23.png?raw=true)

Run the following commands to create the Ansible user and update the Operating System.

```
# Create a new group.
groupadd -g 1001 ansible

# Create a new user.
useradd -g 1001 -u 1001 -m -s /bin/bash ansible

# Create the ssh client directory.
mkdir -m 0700 /home/ansible/.ssh

# Create the authorized_keys files.
cat <<EOF > /home/ansible/.ssh/authorized_keys
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQC01b0KMlayHMYAJF3U/lm7KLeOFIGQ84XLQ60CyjCg28Bcfah0IivrSeDrwVxZS/mRaWNXQv6zWZ1ZcJMJIcE5Y6ZHVFEX6FCzaK3+lqUK0XVbyBKZuGiP5QP4N72jJ7KKHe6MFjhAp3fwN0AF6U7nNXxMIPB/wmwY14xtHHSw8s0aMjqgXB5OBNoK0uAMMzyrP7C1AEW3slaTNMR1439vB5hajpR8bNEK6xFmCF+67QQkqjC9kDnfYPnlx8X94nhvayteGqXHAccOjR+6NW89+BI0Ozzai19DNO9NmyOFbc3IDNQsqPc1Fj6k/prbcoRren8KHrj68xKKuuvFCexqt433FWtmPtE1wTuTQgqDpdLn2U2KOjgZF2YoHek8WzqNOjl2+pJZucrOdpeJ6qvlzsI9/MFR20L7uBx/XgpXW3bQ7tf5k7TXwtGhrhYoxfF7yEuQI2OCo+5445gBrVIRHXmEWKjvavrPUzAJoez+qsNygOb1tBzC2r/vbBdltGs=
EOF

# Ensure ownership is set correctly.
chown -R ansible. /home/ansible/.ssh

# Create the sudoers file for user Ansible.
cat <<EOF > /etc/sudoers.d/administrator-ansible
# Allow Ansible to become root
%ansible ALL=(ALL) NOPASSWD: ALL
EOF

# Check for latest packages.
apt update -yqq

# Upgrade to latest packages.
apt upgrade -yqq

# Restart the server to ensure that we are running the latest kernel.
init 6
```

Your new server is now installed and ready for Ansible.
