
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
    - Disable the root ssh login (Configure SSH Daemon)
        `vi /etc/ssh/sshd_config`
            Change to: `PermitRootLogin no`
            reload: `systemctl reload sshd` (no output)

---

## Part III Install Apache, MySQL, and Php
- ssh root@--.--.--.--
- https://www.digitalocean.com/community/tutorials/how-to-install-linux-apache-mysql-php-lamp-stack-on-centos-7
---
#### Step One — Install Apache
- `sudo yum install httpd`
- Once it installs, you can start Apache on your VPS: `sudo systemctl start httpd.service`
- spot check on your web address: http://your_server_IP_address/
    - Find your web address in terminal: `ip addr show eth0 | grep inet | awk '{ print $2; }' | sed 's/\/.*$//'`
#### Step Two — Install MySQL (MariaDB)
- use yum to acquire & install: `sudo yum install mariadb-server`
- start it: `sudo systemctl start mariadb`
- show status: `sudo systemctl status mariadb` (output should contain: ""Active: active (running)` and the      final line")
- run security script: `sudo mysql_secure_installation`
- enable mariadb on startup: `sudo systemctl enable mariadb`
- Test installation: `mysqladmin -u root -p version` (output should show version,etc.)

#### Step Three — Install PHP
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

- Using the terminal shutdown your server
- Using the Digital Ocean Interface Create a Snapshot of Your Server
- Name it LAMPwithKeys
---

## Part V Install Wordpress
- Guide: https://www.digitalocean.com/community/tutorials/how-to-install-wordpress-on-centos-7
### 1) Create MySQL DB for Wordpress
- Log into MySQL's root: `mysql -u root -p`
    in the Db: (all commands must end with;)
        `CREATE DATABASE wordpress;`
        `CREATE USER wordpressuser@localhost IDENTIFIED BY 'password';`
        `GRANT ALL PRIVILEGES ON wordpress.* TO wordpressuser@localhost IDENTIFIED  BY 'password';`
        `FLUSH PRIVILEGES;` (so that mySQL knows of changes we've made)
        `exit`

### 2) Install Wordpress
- Install PHP Thumbnail module: `sudo yum install php-gd`
- Restart apache so it recogizes new module: `sudo service httpd restart` 
- `sudo yum install wget`
- download & install most recent version of wordpress: 
`cd ~
wget http://wordpress.org/latest.tar.gz`
- Extract files: `tar xzvf latest.tar.gz`
    -  There will now be a directory called "wordpress" in your home directory
- Transfer unpacked files to Apache's document root: `sudo rsync -avP ~/wordpress/ /var/www/html/`
- Create folder for wp to store files: `mkdir /var/www/html/wp-content/uploads`
- Assign correct ownerships & permissions: `sudo chown -R apache:apache /var/www/html/*`
### 3)  Configure WordPress
- move apache root where you moved wp: `cd /var/www/html`
- add sample config file: `cp wp-config-sample.php wp-config.php`
    - if nano not installed: `sudo yum install nano`
- edit config file in nano: `nano wp-config.php`
        - Update the following to your specifics:
            - /** The name of the database for WordPress */
                define('DB_NAME', 'wordpress'));

            /** MySQL database username */
                define('DB_USER', **'wordpressuser'**));

            /** MySQL database password */
                define('DB_PASSWORD', 'password');
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





