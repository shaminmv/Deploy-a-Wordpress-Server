## 1. Deploy your server on Vultr, Digital Ocean or choice of you. 
Deploy your server so that you will be ready to ssh. I will be using Debian 10 for this tutorial. It comes with lots of pre required and even sudo.

## 2. SSH into the server.
I use [cmder](https://cmder.net/) when on windows, else the shell. You are free to use Putty too.

## 3. First thing first, UPDATE the instance
The instance are created from iso images, so better update. ```$ apt-get update```
```$ apt-get upgrade``` or you can run ```apt-get update && apt-get upgrade```

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
```apt-get install -y git zsh curl apt-transport-https```

## 5. Install zsh and make things pretty. :)
Want to know more about zsh? Check [zsh](https://ohmyz.sh/)
```$ sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"```
```$ vim ~/.zshrc``` and change the theme line to what you want, blinks is nice. Reload the shell: ```$ source ~/.zshrc```
If you want a nicer Vim experience, copy .vimrc in this repo to the root's home directory.

## 6. Install Nginx
First, let's check what version is the latest in the apt repository: $ apt-cache show nginx (it is most likely behind) Visit [http://nginx.org/en/linux_packages.html](Nginx repositories page) to see how to add the repository to our apt.

```
$ cat /etc/*-release 
$ echo "deb https://nginx.org/packages/debian/ stretch nginx" > \
  /etc/apt/sources.list.d/nginx.list
$ curl -vs https://nginx.org/keys/nginx_signing.key | apt-key add -
$ apt-get update && apt-get install -y nginx
$ service nginx start
$ ifconfig
```
ifconfig  will give you the ip. Open the Ip on browser and you should see Nginx. If this is the first time you are installing Nginx Congratulations!

## 7. Install Database server
I will be using MariaDB for this but you can use Percona MySQL or MySql of your choice. 
```sudo apt-get install mariadb-server mariadb-client```
After that, run the commands below to secure MariaDB server by creating a root password and disallowing remote root access.
```sudo service mariadb restart```
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

## 8. Install Install PHP / PHP 7.3
```sudo apt -y install php php-common```
Confirm PHP version.
```
hiraschool@HIRA:~$ php -v
PHP 7.3.4-2 (cli) (built: Apr 13 2019 19:05:48) ( NTS )
Copyright (c) 1997-2018 The PHP Group
Zend Engine v3.3.4, Copyright (c) 1998-2018 Zend Technologies
    with Zend OPcache v7.3.4-2, Copyright (c) 1999-2018, by Zend Technologies
```
Next, run the commands below to install PHP 7.3 and related modules.
```sudo apt install php7.3-fpm php7.3-common php7.3-mbstring php7.3-xmlrpc php7.3-soap php7.3-gd php7.3-xml php7.3-intl php7.3-mysql php7.3-cli php7.3-zip php7.3-curl```
After installing PHP 7.3, run the commands below to open PHP default config file for Nginx…
```
sudo vim /etc/php/7.3/fpm/php.ini
```

