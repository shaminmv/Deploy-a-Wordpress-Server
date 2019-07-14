## 1. Deploy your server on Vultr, Digital Ocean or choice of you. 
Deploy your server so that you will be ready to ssh. I will be using Debian 10 for this tutorial. It comes with lots of pre required and even sudo.

## 2. SSH into the server.
I use [cmder](https://cmder.net/) when on windows, else the shell. You are free to use Putty too.

## 3. First thing first, UPDATE the instance
The instance are created from iso images, so better update. ```$ apt-get update```
```$ apt-get upgrade``` or you can run ```apt-get update && apt-get upgrade```

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
w we can install PHP and all the dependencies WordPress needs to run:
$ sudo apt-get update && sudo apt-get install -y \
    imagemagick \
    php7.1-fpm php7.1-mysqli php7.1-curl php7.1-gd php7.1-geoip php7.1-xml \
    php7.1-xmlrpc php7.1-imagick php7.1-mbstring php7.1-ssh2 php7.1-redis

```
## 9. Update Nginx to work with PHP
First, edit the default configuration: $ vim /etc/nginx/nginx.conf

Here, change ```user www-data;``` and ```worker_processes auto;```
```$ vim /etc/nginx/conf.d/default.conf```
Here, change ```server_name _;```, then in ```location /``` block, add ```index.php``` so that the PHP files are read by Nginx. Then uncomment the ```location ~ \.php$``` block and change ```fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;```

Also, in this block, change the ```root /usr/share/nginx/html.```

First check whether Nginx configuration is in order, then reload it:
```
$ service nginx configtest
$ service nginx reload
```





   


