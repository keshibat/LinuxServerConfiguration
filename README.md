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

Disable root login
```
$ sudo vim /etc/ssh/sshd_config
```
```
PermitRootLogin no
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

### Change the SSH port from 22 to 2200
```
Edit the /etc/ssh/sshd_config file to set Port to 2200.
#Restart the service
$ sudo service ssh restart

#Disable port 22
$ sudo ufw deny 22

#Check status of firewall
$ sudo ufw status
```

### Configure the local timezone to UTC
```
$ sudo dpkg-reconfigure tzdata
```

### Install apache2 and libapache2-mod-wsgi modules
```
$ sudo apt-get install apache2 libapache2-mod-wsgi python-dev
```

### Install and configure PostgreSQL
```
$ sudo apt-get install postgresql postgresql-contrib libpq-dev python-dev

#Login as superuser postgres
$ sudo su - postgres

#Create a new database named "catalog" and create a new user named "dbuser" in postgressql.
$ createdb catalog;
$ createuser dbuser;

#Set password for dbuser
$ alter role dbuser with password 'Secret1.0';

#Give user "dbuser" permission to "catalog" application database
$ grant all privileges on database catalog to dbuser;
```


### Install Git
```
$ sudo apt-get install git
$ git config --global user.name "keshibat"
$ git config --global user.email "kensukeshibata@gmail.com"
```

### Install mod_wsgi
```
# install mod_wsgi
$ sudo apt-get install libapache2-mod-wsgi libapache2-mod-wsgi-py3

#Configure Apache to handle requests using the WSGI module
# Add the following line at the end of the <VirtualHost *:80> block, right before the closing </VirtualHost>
line: WSGIScriptAlias / /var/www/html/myapp.wsgi

#Restart Apache
$ sudo apache2ctl restart
```


### Clone the Catalog app from Github
```
$ sudo mkdir /var/www/catalog

#Change owner for the catalog folder
$ sudo chown -R grader:grader /var/www/catalog

#Clone the catalog repository from Github
$ cd /var/www/catalog
$ git clone https://github.com/keshibat/Item_Catalog.git catalog

#Make a catalog.wsgi file: Directory:/var/www/catalog
```
```python
#!/usr/bin/python
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0,"/var/www/catalog/")

from catalog import app as application
```

### Install virtual environment, Flask and the project's dependencies
```
# Install pip to use pip to install virtualenv and Flask
$ sudo apt-get install python-pip

# Install virtualenv
$ sudo pip install virtualenv

# Move to the catalog folder
$ cd /var/www/catalog

# Create a new virtual environment
$ sudo virtualenv venv

# Activate the virtual environment:
$ source venv/bin/activate

# Change permissions to the virtual environment folder
$ sudo chmod -R 777 venv

# Install Flask
$ pip install Flask

# Install all the other project's dependencies
$ pip install bleach httplib2 request oauth2client sqlalchemy python-psycopg2
```



### Install virtual environment, Flask and the project's dependencies
```
$ sudo bash
$ apt-get -qqy install make zip unzip postgresql
$ apt-get -qqy install python3 python3-pip
#Upgrade pip
$ pip3 install
$ pip3 install flask packaging oauth2client redis passlib flask-httpauth
$ pip3 install sqlalchemy flask-sqlalchemy psycopg2-binary bleach requests
$ apt-get -qqy install python python-pip
#Upgrade pip
$ pip2 install
$ pip2 install flask packaging oauth2client redis passlib flask-httpauth
$ pip2 install sqlalchemy flask-sqlalchemy psycopg2-binary bleach requests


# Install virtualenv
$ sudo apt install virtualenv
# Move to the catalog folder
$ cd /var/www/catalog
# Create a new virtual environment
$ sudo virtualenv venv
# Activate the virtual environment
$ source venv/bin/activate
# Change permissions to the virtual environment folder
$ sudo chmod -R 777 venv
```



### Configuring and enable a new virtual host
```
# Create a virtual host conifg file
$ sudo nano /etc/apache2/sites-available/000-default.conf
```
```
<VirtualHost *:80>
    ServerName 54.252.247.6
    ServerAdmin admin@54.252.247.6
    WSGIScriptAlias / /var/www/catalog/catalog.wsgi
    <Directory /var/www/catalog/catalog>
        Order deny,allow
        Allow from all
    </Directory>
    Alias /static /var/www/catalog/catalog/static
    <Directory /var/www/catalog/catalog/static/>
        Order allow,deny
        Allow from all
    </Directory>
    ErrorLog ${APACHE_LOG_DIR}/error.log
    LogLevel warn
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```


## References
  1. [Amazon Lightsail](https://lightsail.aws.amazon.com)
  2. [mod_wsgi (Apache)](http://flask.pocoo.org/docs/0.12/deploying/mod_wsgi/)
  3. [Deploy a Flask Application on an Ubuntu](https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps#step-two-%E2%80%93-creating-a-flask-app)
