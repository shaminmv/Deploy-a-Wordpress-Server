## 1. Deploy your server on Vultr, Digital Ocean or choice of you. 
Deploy your server so that you will be ready to ssh. I will be using Debian 8 strech for this tutorial.

## 2. SSH into the server.
I use [cmder](https://cmder.net/) when on windows, else the shell. You are free to use Putty too.

## 3. First thing first, UPDATE the instance
The instance are created from iso images, so better update. ```$ apt-get update```
```$ apt-get upgrade``` or you can run ```apt-get update && apt-get upgrade```

# 4. Install some components we will be needing on the way.
Debian already comes with lots of pre install component but we need some like zsh and git. Lets do it
```apt-get install -y git zsh curl apt-transport-https``

