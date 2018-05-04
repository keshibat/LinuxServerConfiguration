# Linux-Server-Configuration

## About this project

This is the final project of Udacity's [Full Stack Web Development Nanodegree](https://www.udacity.com/course/full-stack-web-developer-nanodegree--nd004) that contains the code and config for this [Clothing Catalog App](https://github.com/keshibat/Item_Catalog), built using Python Flask, to run on a Linux Ubuntu server with Apache2, hosted on a Virtual Private Server with [Amazon LightSail](https://amazonlightsail.com/).



## IP address and ssh details

IP:     54.252.247.6

SSH:    `ssh grader@54.252.247.6 -p 2200 -i ~/.ssh/LinuxCourse`



## Summary of Configuration

### Update all packages
Keep your software up to date with new release

```bash
$ sudo apt-get update

$ sudo apt-get upgrade

sudo apt-get autoremove
```


### Create new user
Create new user "grader"

```bash
$ sudo adduser grader
```

Give new user "grader" the permission to sudo.

Create new file in etc/sudoers.d directory
```bash
$ touch /etc/sudoers.d/grader
```
Edit new file
```bash
$ vim /etc/sudoers.d/grader
```
File:grader in path '/etc/sudoers.d'
```
grader ALL=(ALL) NOPASSWD:ALL

```
```
$  sudo usermod -aG sudo grader
```

###Create an SSH key pair for grader using the ssh-keygen tool
The key is generated on the local machine using **ssh-keygen** and stored in a file named LinuxCourse.

local Machine
```
$ pwd
/Users/username/.ssh
$ ssh-keygem
```

Two file would be created
```
LinuxCourse.pub
LinuxCourse
```

place public key on a sever
```bash
$ su - grader
$ mkdir .ssh
$ vim .ssh/authorized_keys
```
Copy and paste content of LinuxCourse.pub in .ssh/authorized_keys
```bash
$ chmod 700 .ssh
$ chmod 644 .ssh/authorized_keys
```

Disable the password base logins so that all of users to only be able to login using key pairs

```bash
$ sudo vim /etc/ssh/sshd_config
```
```
PasswordAuthentication no
```
Restart Service
```bash
sudo service ssh restart
```

##Firewall

```bash
# set default rule to deny all incoming connections
$ sudo ufw default deny incoming

# set default rule to allow all outgoing connections
$ sudo ufw default allow outgoing

# Allow connections for SSH (port 2200), HTTP (port 80), and NTP (port 123):

$ sudo ufw allow ssh

$ sudo ufw allow 2200

$ sudo ufw allow 123

$ sudo ufw allow 80

# allow http
$ sudo ufw allow www

# enable the firewall
$ sudo ufw enable

# verify config and status (see example below)
$ sudo ufw status verbose
```

## Change the SSH port from 22 to 2200
```
Edit the /etc/ssh/sshd_config file to set Port to 2200.
#Restart the service
$ sudo service ssh restart

#Disable port 22
$ sudo ufw deny 22

#Check status of firewall
$ sudo ufw status
```




