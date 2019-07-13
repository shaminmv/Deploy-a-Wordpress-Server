## 1. Deploy your server on Vultr, Digital Ocean or choice of you. 
Deploy your server so that you will be ready to ssh. I will be using Debian 8 strech for this tutorial.

## 2. SSH into the server.
I use [cmder](https://cmder.net/) when on windows, else the shell. You are free to use Putty too.

## 3. First thing first, UPDATE the instance
The instance are created from iso images, so better update. ```$ apt-get update```
```$ apt-get upgrade``` or you can run ```apt-get update && apt-get upgrade```

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
