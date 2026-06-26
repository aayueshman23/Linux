# Linux Starting ...
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Phase 1: Build the VM -
Install a Linux VM, preferably Ubuntu Server or CentOS on VirtualBox.
Allocate enough resources for practice: 2 CPU, 2–4 GB RAM, and 20–40 GB disk.
Take a clean snapshot before you start.
Update the OS immediately after first boot. On Ubuntu use sudo apt update && sudo apt upgrade -y; on RHEL-based systems use sudo dnf update -y then sudo reboot.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Phase 2: Basic server setup -
Login as root with sudo su -.
Set hostname and timezone. (hostnamectl set-hostname centos-vm01, hostnamectl && timedatectl set-timezone Asia/Kolkata , timedatectl) // 
Create one admin user for daily work instead of using root.(useradd username, passwd password)
Add that user to the sudo group or wheel group, depending on distro.(usermod -aG wheel devops)
Confirm you can log in as the new user and run sudo whoami.(which should return root)
Note Imp Points ->
On a CentOS VM, the simplest command to list users is: cut -d: -f1 /etc/passwd
To switch to another user in Linux, use: su - username  (su - switch user)
If you want to go back to your previous user: exit.
sudo useradd username - creates a new user account.
sudo usermod -aG sudo username - adds an existing user to the sudo group so they can run admin commands which can be done from any root account

-------------------------------------------------------------------------------------------------------------------------------------------------------------------
Phase 3: Users, groups, and SSH -
Create a few test users and groups, such as dev, qa, and ops.
Assign users to groups and practice groups, id, usermod, and groupadd.
Generate an SSH key on your local machine.
Copy the public key to the VM and test key-based login.
Disable password SSH login after confirming key login works.

Solution -
1) Create groups
Run these as root or with sudo:


sudo groupadd dev
sudo groupadd qa
sudo groupadd ops
Check:


getent group dev
getent group qa
getent group ops
2) Create users
Create users with home directories and  shell:


sudo useradd -m -s /bin/ alice
sudo useradd -m -s /bin/ bob
sudo useradd -m -s /bin/ charlie
Set passwords:


sudo passwd alice
sudo passwd bob
sudo passwd charlie
Check users:


id alice
id bob
id charlie

3) Add users to groups
Example assignment:

alice → dev

bob → qa

charlie → ops

Commands:


sudo usermod -aG dev alice
sudo usermod -aG qa bob
sudo usermod -aG ops charlie
Verify:


groups alice
groups bob
groups charlie
On your local computer:

4) Create an SSH key on your local machine

ssh-keygen -t ed25519 -C "yourname@example.com"
Press Enter to accept the default file location.
If you want a password on the key, set a passphrase; otherwise press Enter twice.

Check the public key:


cat ~/.ssh/id_ed25519.pub
5) Copy the public key to the VM
If you want to allow SSH login for alice, copy your public key to her account.

From your local machine:


ssh-copy-id alice@VM_IP_ADDRESS
If ssh-copy-id is not available, do it manually:

On the VM
Login as root or an admin user, then:


sudo mkdir -p /home/alice/.ssh
sudo nano /home/alice/.ssh/authorized_keys
Paste your public key into that file, save, and exit.

Set permissions:


sudo chown -R alice:alice /home/alice/.ssh
sudo chmod 700 /home/alice/.ssh
sudo chmod 600 /home/alice/.ssh/authorized_keys
6) Test SSH key login
From your local machine:


ssh alice@VM_IP_ADDRESS
If the key is working, you should log in without typing the account password.

7) Disable password SSH login
Only do this after key login is confirmed.

On the VM, edit SSH config:


sudo nano /etc/ssh/sshd_config
Set or change these lines:


PasswordAuthentication no
PubkeyAuthentication yes
Then restart SSH:


sudo systemctl restart sshd
Check status:


sudo systemctl status sshd
8) Verify it still works
Open a new terminal on your local machine and test:


ssh alice@VM_IP_ADDRESS
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------


