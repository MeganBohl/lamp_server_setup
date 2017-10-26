
# Document a LAMP setup and Save the Image
## LAMP stack Install on CENTOS07 (Digital Ocean)

## Part I: Setup Local and Remote Repository
---

### Create a Local Git Repo
- Create a new repo on your laptop called docs
- Inside docs start a textfile called lamp_server_setup.md
    `cd /Users/meganbohl/Desktop/docs`
    `touch lamp_server_setup.md`
    `git init`
---

### Create a New GitHub Repository Called lamp_server_setup
- https://github.com/MeganBohl?tab=repositories, click "new"
- Make sure the repository is created empty (no readme)

---

### Connect Your Local Repo To Your GitHub Repo
- `git remote add origin https://github.com/MeganBohl/lamp_server_setup.git`
- `git push -u origin master`
    -correct response will be: "Branch master set up to track remote branch master from origin."
---

### Check Your GitHub Repo
- create a new directory elsewhere called test_lamp
- `cd /Users/meganbohl/Desktop/test_lamp`
- `git clone git clone https://github.com/MeganBohl/lamp_server_setup.git`
---

### Demo to Instructor Part I is complete

## Part II Setup an Image in the Cloud (Digital Ocean Droplet)
- Guide: https://www.digitalocean.com/community/tutorials/initial-server-setup-with-centos-7
---

### Create a Droplet and Reset the Root Password
- Create a new droplet
- Use SSH to login as root (use the emailed root password)
- Change the root password
- Write down the root password
- Confirm to instructor you can log in and out with the root password
---

### Add a Second User
- Add a second user and set the password 
- `adduser <megan> passwd <username>`
- write down the username and password done
- login and backout to test the username
    - give user root privileges: `gpasswd -a megan wheel`
---

### Create SSH Keys Locally and Install Public Key on the server
- Create SSH keys locally
    `ssh-keygen` (for new install), already created key pair,
        `ssh-copy-id megan@45.55.224.80`
- Copy the public to your new server 
- Ensure you can login to your new user with the SSH key -YES

- Disable the root login
- Add Public Key to New Remote User
    - as root, go to new user dir: `su - megan`
    - Create new directory .ssh,restrict permissions:
    `mkdir .ssh`
    `chmod 700 .ssh`
    - Now open a file in .ssh/vi, called authorized keys:
    `vi .ssh/authorized_keys`
        ssh will paste into vi, hit "i" for insert mode, esc i mode, :x
    -restrict permissions of autorized keys file: `chmod 600 .ssh/authorized_keys`
    - `exit` once to root
---

## Part III Install Apache, MySQL, and Php
- ssh root@--.--.--.--
- https://www.digitalocean.com/community/tutorials/how-to-install-linux-apache-mysql-php-lamp-stack-on-centos-7
---
####Step One — Install Apache
- `sudo yum install httpd`
- Once it installs, you can start Apache on your VPS: `sudo systemctl start httpd.service`
- spot check on your web address: http://your_server_IP_address/
    - Find your web address in terminal: `ip addr show eth0 | grep inet | awk '{ print $2; }' | sed 's/\/.*$//'`
####Step Two — Install MySQL (MariaDB)
- use yum to acquire & install: `sudo yum install mariadb-server mariadb`
- start it: `sudo systemctl start mariadb`
- run security script: `sudo mysql_secure_installation`
- enable mariadb on startup: `sudo systemctl enable mariadb.service`

####Step Three — Install PHP
- `sudo yum install php php-mysql`
- restart apache to run w/PHP: `sudo systemctl restart httpd.service`
- Optional Modules (research script to add all) `yum search php-`  install: `yum install package 1`
    - Test PHP w/script in vi: `sudo vi /var/www/html/info.php` (in Vi, I:Insert, EXIT `shift :x`)
        - Type in the following in vi editor: `<?php phpinfo(); ?>`
        - Run these commands to allow traffic through firewall: 
            `sudo firewall-cmd --permanent --zone=public --add-service=http`
            `sudo firewall-cmd --permanent --zone=public --add-service=https`
            `sudo firewall-cmd --reload`
        - Test script at: http://your_server_IP_address/info.php
    - Remove php info. file to prohibit info. being passed through server: `sudo rm /var/www/html/info.php`
    ***DP did "Hello World" instead
---

## Part IV Creating a Snapshot of your image
- Megan original:Take snapshot on digital ocean of completed Lamp stack Titled:               "centos-512mb-Lampsetup"
David's instruction:
- Using the terminal shutdown your server
- Using the Digital Ocean Interface Create a Snapshot of Your Server
- Name it LAMPwithKeys
---

## Part V Install Wordpress
- Guide: https://www.digitalocean.com/community/tutorials/how-to-install-wordpress-on-centos-7
- Do you need wget ? yum install wget
- Test Wordpress
- Create Another Snapshot call it WordPressImage
---

## Part VI Add Encryption with OpenSSL
- Guide: https://www.digitalocean.com/community/tutorials/how-to-create-a-self-signed-ssl-certificate-for-apache-in-ubuntu-16-04
- The guide is for Ubuntu, but should work for CentOS
- Create a final snapshot called WPwithEncryption
---

## Part VII
- Create a new droplet based on your final snapshot 
- Test your WordPress and Encryption by browsing to https://your_ip_address
---





