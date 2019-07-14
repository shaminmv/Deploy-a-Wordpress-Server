## 1. Deploy your server on Vultr, Digital Ocean or choice of you. 
Deploy your server so that you will be ready to ssh. I will be using Debian 10 for this tutorial. It comes with lots of pre required and even sudo.

## 2. SSH into the server.
I use [cmder](https://cmder.net/) when on windows, else the shell. You are free to use Putty too.

## 3. First thing first, UPDATE the instance
The instance are created from iso images, so better update. ```$ apt-get update```

Now install sudo
``` apt-get install sudo ```

Create a new user account using the adduser command. Don’t forget to replace username with your desired user name:

```adduser username```
The command will prompt you to set and confirm the new user password. Make sure that the password for the new account is as strong as possible (combination of letters, numbers and special characters).
```
root@HIRA:~# adduser hiraschool
Adding user `hiraschool' ...
Adding new group `hiraschool' (1000) ...
Adding new user `hiraschool' (1000) with group `hiraschool' ...
Creating home directory `/home/hiraschool' ...
Copying files from `/etc/skel' ...
New password:
Retype new password:
passwd: password updated successfully
Changing the user information for hiraschool
Enter the new value, or press ENTER for the default
        Full Name []: Hira School
        Room Number []:
        Work Phone []:
        Home Phone []:
        Other []:
Is the information correct? [Y/n] Y
```

Now Add the user to the sudo group
By default on Debian systems, members of the group sudo are granted with sudo access. To add a user to the sudo group use the usermod command:
```usermod -aG sudo username```

Exit and close the session and connect as the user you created.

Use the sudo command to run the whoami command:
```
sudo whoami
hiraschool@HIRA:~$ sudo whoami

We trust you have received the usual lecture from the local System
Administrator. It usually boils down to these three things:

    #1) Respect the privacy of others.
    #2) Think before you type.
    #3) With great power comes great responsibility.

[sudo] password for hiraschool:
[sudo] password for hiraschool:
root
```


## 4. Install some components we will be needing on the way.
Debian already comes with lots of pre install component but we need some like zsh and git. Lets do it
```sudo apt-get install -y git zsh curl apt-transport-https```

## 5. Install zsh and make things pretty. :)
Want to know more about zsh? Check [zsh](https://ohmyz.sh/)
```$ sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"```
```$ vim ~/.zshrc``` and change the theme line to what you want, blinks is nice. Reload the shell: ```$ source ~/.zshrc```
If you want a nicer Vim experience, copy .vimrc in this repo to the root's home directory.

## 6. Install Nginx
First, let's check what version is the latest in the apt repository: $ apt-cache show nginx (it is most likely behind) Visit [http://nginx.org/en/linux_packages.html](Nginx repositories page) to see how to add the repository to our apt.

```
$ cat /etc/*-release 
$ echo "deb https://nginx.org/packages/debian/ stretch nginx" > sudo \
  /etc/apt/sources.list.d/nginx.list
$  curl -vs https://nginx.org/keys/nginx_signing.key | sudo apt-key add -
$ sudo apt-get update && sudo apt-get install -y nginx
$ sudo service nginx start
$ ifconfig
```
ifconfig  will give you the ip. Open the Ip on browser and you should see Nginx. If this is the first time you are installing Nginx Congratulations!

## 7. Install Database server
I will be using MariaDB for this but you can use Percona MySQL or MySql of your choice. 
```sudo apt-get install mariadb-server mariadb-client```
After that, run the commands below to secure MariaDB server by creating a root password and disallowing remote root access.
```
sudo mysql_secure_installation
```
To test if MariaDB is installed, type the commands below to logon to MariaDB server
```sudo mysql -u root -p```
```
hiraschool@HIRA:~$ sudo mysql -u root -p

Enter password:
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 36
Server version: 10.3.15-MariaDB-1 Debian 10

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]>
```
Restart MariaDB server
```sudo systemctl restart mysql.service```


## 8. Install Install PHP
Just like with Nginx, we need to create two files: the apt source file, which points to the repository and key file, which is used to verify the integrity of the repository:
```
$ echo "deb https://packages.sury.org/php/ stretch main" > sudo \
  /etc/apt/sources.list.d/php.list
$ sudo curl -o /etc/apt/trusted.gpg.d/php.gpg https://packages.sury.org/php/apt.gpg
```
Add ondrej/php PPA
```
sudo apt install apt-transport-https lsb-release ca-certificates
sudo wget -O /etc/apt/trusted.gpg.d/php.gpg https://packages.sury.org/php/apt.gpg
sudo sh -c 'echo "deb https://packages.sury.org/php/ $(lsb_release -sc) main" > /etc/apt/sources.list.d/php.list'
sudo apt update
```
Now we can install PHP and all the dependencies WordPress needs to run:
```

sudo apt install php7.2-fpm php7.2-common php7.2-mbstring php7.2-xmlrpc php7.2-soap php7.2-gd php7.2-xml php7.2-intl php7.2-mysql php7.2-cli php7.2-zip php7.2-curl
```
## 9. Update Nginx to work with PHP




```sudo nano /etc/php/7.2/fpm/php.ini```

Then make the changes on the following lines below in the file and save. The value below are great settings to apply in your environments.

```
file_uploads = On
allow_url_fopen = On
memory_limit = 256M
upload_max_filesize = 100M
cgi.fix_pathinfo=0
max_execution_time = 360
date.timezone = America/Chicago
```
If you are getting nginx timeout error ; you may want to increase max_execution_time to may be 290000 ?
Now reload PHP-FPM: ```sudo systemctl restart nginx.service
sudo systemctl restart php7.2-fpm.service```

## 10. Create Wordpress DB
Now that you’ve install all the packages that are required, continue below to start configuring the servers. First run the commands below to create WordPress database.

Run the commands below to logon to the database server. When prompted for a password, type the root password you created above.
``` sudo mysql -u root -p ```
Then create a database called wordpress
``` CREATE DATABASE wordpress; ```
Create a database user called wordpressuser with new password
``` CREATE USER 'wordpressuser'@'localhost' IDENTIFIED BY 'new_password_here';```
Then grant the user full access to the database.
``` GRANT ALL ON wordpress.* TO 'wordpressuser'@'localhost' IDENTIFIED BY 'user_password_here' WITH GRANT OPTION; ```

 ## 11. Install Wordpress
 First create a directory that will hold the WordPress core for your domain: ```$ sudo mkdir -p /var/www/html/example.com ``` (change with your domain)

Then, navigate to the Release archive and right click latest tar.gz and choose copy link address.

```
$ cd /var/www/html/example.com
$ sudo wget https://wordpress.org/latest.tar.gz
$ sudo tar -zxf latest.tar.gz
$ sudo mv wordpress/* .
$ sudo rm -rf wordpress latest.tar.gz
```

 
## 12. Then run the commands below to set the correct permissions for WordPress to function.
```
sudo chown -R www-data:www-data /var/www/html/wordpress/
sudo chmod -R 755 /var/www/html/wordpress/
```

## 13. Configure Nginx HTTP Server
Finally, configure Apahce2 site configuration file for WordPress. This file will control how users access WordPress content. Run the commands below to create a new configuration file called wordpress
``` sudo vim /etc/nginx/sites-available/wordpress ```

Then copy and paste the content below into the file and save it. Replace the highlighted line with your own domain name and directory root location.

```
server {
    listen 80;
    listen [::]:80;
    root /var/www/html/wordpress;
    index  index.php index.html index.htm;
    server_name  example.com www.example.com;

     client_max_body_size 100M;

    location / {
        try_files $uri $uri/ /index.php?$args;
    }

    location ~ \.php$ {
         include snippets/fastcgi-php.conf;
         fastcgi_pass unix:/var/run/php/php7.2-fpm.sock;
         fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
         include fastcgi_params;
    }
}
```
Save the file and exit.

## 14. Enable WP Site
After configuring the VirtualHost above, enable it by running the commands below, then restart Nginx server…
``` sudo ln -s /etc/nginx/sites-available/wordpress /etc/nginx/sites-enabled/ ```

Reload ngix , before reloading always run a config test. Now if your domain is pointed to the server you can view the wp config page. Congrats

## 15. Configure Wordpress
Now that Nginx is configured, run the commands below to create WordPress wp-config.php file.
``` sudo mv /var/www/html/wordpress/wp-config-sample.php /var/www/html/wordpress/wp-config.php ```

Then run the commands below to open WordPress configuration file.
``` sudo vim /var/www/html/wordpress/wp-config.php ```

Edit the db settings
```
// ** MySQL settings - You can get this info from your web host ** //
/** The name of the database for WordPress */
define('DB_NAME', 'wpdb');

/** MySQL database username */
define('DB_USER', 'wpuser');

/** MySQL database password */
define('DB_PASSWORD', 'user_password_here');

/** MySQL hostname */
define('DB_HOST', 'localhost');

/** Database Charset to use in creating database tables. */
define('DB_CHARSET', 'utf8');

/** The Database Collate type. Don't change this if in doubt. */
define('DB_COLLATE', '');
```

## Step 16: Install Let’s Encrypt Client
To get Let’s Encrypt free SSL/TLS certificates on your Ubuntu machine, you should first install its client. The client helps automate the process for you. To install it, run the commands below.

``` sudo apt-get install python-certbot-nginx```

## Step 17: Obtaining your free SSL/TLS Certificates
After installing Let’s Encrypt Certbot client module for Nginx, run the commands below to obtain your free Let’s Encrypt SSL/TLS certificate the domain specified… make sure to replace example.com with your own domain..
``` sudo certbot --nginx -m admin@example.com -d example.com -d www.example.com ```
accept the TOS, If you want to share email share else choose N, and the last step is very important.
```
e choose whether or not to redirect HTTP traffic to HTTPS, removing HTTP access.
-------------------------------------------------------------------------------
1: No redirect - Make no further changes to the webserver configuration.
2: Redirect - Make all requests redirect to secure HTTPS access. Choose this for
new sites, or if you're confident your site works on HTTPS. You can undo this
change by editing your web server's configuration.
-------------------------------------------------------------------------------
Select the appropriate number [1-2] then [enter] (press 'c' to cancel): 2
```
Pick option 2 to redirect all traffic over HTTPS. This is important!

After that, the SSL client should install the cert and configure your website to redirect all traffic over HTTPS.
```
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Congratulations! You have successfully enabled https://hira.edu.mv and
https://www.hira.edu.mv

You should test your configuration at:
https://www.ssllabs.com/ssltest/analyze.html?d=hira.edu.mv
https://www.ssllabs.com/ssltest/analyze.html?d=www.hira.edu.mv
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

IMPORTANT NOTES:
 - Congratulations! Your certificate and chain have been saved at:
   /etc/letsencrypt/live/hira.edu.mv/fullchain.pem
   Your key file has been saved at:
   /etc/letsencrypt/live/hira.edu.mv/privkey.pem
   Your cert will expire on 2019-10-12. To obtain a new or tweaked
   version of this certificate in the future, simply run certbot again
   with the "certonly" option. To non-interactively renew *all* of
   your certificates, run "certbot renew"
 - Your account credentials have been saved in your Certbot
   configuration directory at /etc/letsencrypt. You should make a
   secure backup of this folder now. This configuration directory will
   also contain certificates and private keys obtained by Certbot so
   making regular backups of this folder is ideal.
 - If you like Certbot, please consider supporting our work by:

   Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
   Donating to EFF:                    https://eff.org/donate-le
 ```
 Your sites config file will be updated by Let's Encrypt certbot. Check :
 ```sudo vim /etc/nginx/sites-available/hira.edu.mv```
 You should see
 ```
   listen 443 ssl; # managed by Certbot
    ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem; # managed by Certbot
    include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot

    if ($scheme != "https") {
        return 301 https://$host$request_uri;
    } # managed by Certbot

    # Redirect non-https traffic to https
    # if ($scheme != "https") {
    #     return 301 https://$host$request_uri;
    # } # managed by Certbot
```
You’ll have to manually renew the certificates after every 3 months. You’ll get an email reminder to reset when the certificates are about to expire. To test the renewal process run the commands below.
``` sudo certbot renew --dry-run ```
To setup a process to automatically renew the certificates, add a cron job to execute the renewal process.
``` sudo crontab -e ```







 






   


