# Linux Server Configuration -- Udacity Full-Stack Web Developer Nanodegree Program

This project configures a linux server to let through various ports, defend against security attacks, and deploy a previous project.
This README does not include every step used, it is a simple summary of the configuration changes made and the installation steps used.

## Access

IP Address : 3.89.241.137
SSH port : 2200
URL : http://3.89.241.137.xip.io 

## Configuration Changes Made and Installations

#### Update all packages
```sudo apt-get update```
```sudo apt-get upgrade```

#### Change SSH port to 2200
```sudo nano /etc/ssh/sshd_config```
Change port from 22 to 2200
Save and exit
```sudo service sshd restart```

#### Configure Uncomplicated Firewall
```sudo ufw default deny incoming```
```sudo ufw default allow outgoing```
```sudo ufw allow 2200/tcp```
```sudo ufw allow 80/tcp```
```sudo ufw allow 123/udp```
```sudo ufw enable```
Check by running ```sudo ufw status```

#### Create user grader
```sudo adduser grader```
Give grader permission to sudo
```sudo ls /etc/sudeoers.d```
```sudo nano /etc/sudoers.d/grader```
Add this line:
```ALL=(ALL) NOPASSWD:ALL```

##### Create SSH key pair for grader
Run ```ssh-keygen```
Public key was placed in ```.ssh/authorized_keys```
Private key was generated locally
Public 
Grader can now be accessed using
```ssh grader@3.89.241.137 -p 2200 ~i /.ssh/linuxcourse```

##### Check password authentication
To allow for proper login of users, run
```sudo nano /etc/ssh/sshd_config```
PasswordAuthentication should be set to no

#### Configure timezone
```sudo dpkg-reconfigure tzdata```
Select UTC

### Install PostgreSQL
```sudo apt-get install postgresql postgresql-contrib```

#### Create new database user
Connect to psql ```sudo -u postgres psql```
Create user Catalog
Create database Catalog
Give user Catalog sole permission to the database

### Install Apache and mod_wsgi
```sudo apt-get install apache2```
I used Python2.7 for this project, so I downloaded the appropriate version of mod_wsgi
```sudo apt-get install libapache2-mod-wsgi```
Enable it
```sudo a2enmod wsgi```

### Install Git
```sudo apt-get install git```

### Deploy Catalog Project

I utilized the digital ocean tutorial heavily, the link is below.
Inside ```/var/www```, run ```sudo mkdir catproj```
Inside this directory, 
```sudo git clone https://github.com/chandragan22/catalog-project.git```
In order to install all necessary programs, install pip
```sudo apt-get install python-pip```
Install all other programs such as Flask using
```sudo pip install Flask```
Change the name of project.py to __init__.py
#### Configure Virtual Host
Configure the virtual host with the name of the server, where the WSGIScript should be fetched from, where the directory of the files is, and various other configurations.
```sudo nano /etc/apache2/sites-available/catproj.conf```
```
<VirtualHost *:80>
		ServerName 3.89.241.137.xip.io
		ServerAdmin admin@3.89.241.137
		WSGIScriptAlias / /var/www/catproj/catproject.wsgi
		<Directory /var/www/catproj/catproj/>
			Order allow,deny
			Allow from all
		</Directory>
		Alias /static /var/www/catproj/catproj/static
		<Directory /var/www/catproj/catproj/static/>
			Order allow,deny
			Allow from all
		</Directory>
		ErrorLog ${APACHE_LOG_DIR}/error.log
		LogLevel warn
		CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>```
Enable the virtual host
```sudo a2ensite catproj```
#### Utilize mod_wsgi
In ```/var/www/catproj```, create the catproject.wsgi file 
Add these lines
```#!/usr/bin/python
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0,"/var/www/catproj/")

from catproj import app as application
application.secret_key = 'super_secret_key' ```


##### Configure OAuth for Google log in
Update the javascript and redirect url for the OAuth Credentials
Run ```sudo nano client_secrets.json``` in /var/www/catproj/catproj
Copy the contents from the downloaded client_secrets file into the client_secrets.json file
Update the clientID in the login.html page with the updated clientID

##### Configure python files to use PostgreSQL
Use PostgreSQL in database_setup.py and __init__.py with these lines

Run ```sudo service apache2 restart```

Visit the link above for the deployed project

## Third Party resources

These were used to fully understand configuration changes and the best way to go about these changes.

The Udacity Full Stack Developer Course was fully used to understand the content. Its discussion forums and mentorship modules were also utilized.
http://suite.opengeo.org/docs/latest/dataadmin/pgGettingStarted/firstconnect.html 
https://git-scm.com/book/en/v2/Getting-Started-Installing-Git 
https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps 
https://www.codementor.io/abhishake/minimal-apache-configuration-for-deploying-a-flask-app-ubuntu-18-04-phu50a7ft 
https://medium.com/coding-blocks/creating-user-database-and-adding-access-on-postgresql-8bfcd2f4a91e 
http://flask.pocoo.org/docs/0.12/deploying/mod_wsgi/ 
https://docs.sqlalchemy.org/en/latest/core/engines.html 
https://www.digitalocean.com/community/tutorials/how-to-set-up-apache-virtual-hosts-on-ubuntu-14-04-lts
https://wixelhq.com/blog/how-to-install-postgresql-on-ubuntu-remote-access   


