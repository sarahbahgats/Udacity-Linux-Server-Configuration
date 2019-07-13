# Udacity-Linux-Server-Configuration
Udacity Full Stack Web Developer nanodegree third project

#1 Project description

This project is the third and last project of Full Stack Web Developer Nanodegree; in this project, 
student will take a baseline installation of a Linux server and prepare it to host web applications. 
Also, Student will secure server from a number of attack vectors, install and configure a database server, and deploy one of existing web applications onto it. 

Following to this description, there will be a summary about  IP address, URL, summary of software installed, summary of configurations made, and a list of third-party resources used to complete this project.

IP address: 3.9.175.130
URL: http://3.9.175.130/
Accessible SSH port: 2200
SSH Key/Grader Key: ssh -i lightsail-udacity.pem grader@3.9.175.130 -p 2200 

#2 Summary of software used 

*Linux distribution: Ubuntu 16.04.4 LTS.
*Virtual private server: Amazon Lightsail.
*Web application: Item Catalog project - Second project of this Nanodegree degree.
*The database server is PostgreSQL.
*Local machine: MacBook Pro.

#3 configurations changes made
1- To access SSH through the instance:
Download the Private Key of the instance made on Amazon Lightsail,
Save the key in the working directory and open terminal into it,
Create .ssh directory via sudo mkdir .ssh
Move the key to the ~/.ssh directory sudo mv key.pem ~/.ssh
Connect to the instance ssh -i ~/.ssh/key.pem [user-name]@[public-ip]
2- Update the installed packages:
Update Packages via sudo apt-get update
Upgrade Packages via sudo apt-get upgrade
3- Change SSH port from 22 to 2200:
Open the instance and add the port 2200 to the networking part,
Open the nano editor via sudo nano /etc/ssh/sshd_config and change Port 22 to Port 2200, to save press CTRL+X then yes,
Restart the service via visudo service ssh restart.
4- Configure the Uncomplicated Firewall (UFW):
SSH (port 2200) via sudo ufw allow 2200/tcp
HTTP (port 80) via sudo ufw allow 80/tcp
NTP (port 123) via sudo ufw allow 123/udp
Enable the changes via sudo ufw enable
Restart the service via sudo service ssh restart
5- Give the user grader access to the server:
Create a new user account called grader via sudo adduser grader
Give grader the permission to sudo via sudo nano /etc/sudoers.d/grader and add this line grader ALL=(ALL:ALL) ALL to nano editor then save and close,
Access with grader user via  su -l grader
Create an SSH key pair for grader using the ssh-keygen tool
Open a new terminal into the working directory,
Create SSH key pair using ssh-keygen via ssh-keygen give it name grader_key
open the key file sudo cat ~/.ssh/grader_key and take it copy
Go to the grader terminal and create .ssh directory via
sudo mkdir .ssh then cd .shh
sudo nano authorized_key then paste the key into it then save and close
sudo chmod 700 .ssh
sudo chmod 644 .ssh/authorized_keys
sudo service ssh restart
Log  into the server with grader user via ssh -i lightsail-udacity.pem grader@3.9.175.130 -p 2200
6- Prepare to deploy Item-Catalog project
Change the local timezone to UTC via sudo dpkg-reconfigure tzdata then choose None of the above then choose UTC
Install and configure Apache to serve a Python mod_wsgi application via
Install Apache2 sudo apt-get install apache2
Install mod_wsgi sudo apt-get install libapache2-mod-wsgi-py3
Enable mod_wsgi  via sudo a2enmod wsgi
Start the web server via sudo service apache2 start
Install and configure PostgreSQL to add a database called catalog via
sudo apt-get install postgresql
Switch to postgres, PostgreSQL user sudo su - postgres
Create database user called catalog postgres=# CREATEDB catalog;
Create a new user postgres=# CREATE USER catalog;
Set a password for catalog user �Udacity�
Then exit psql
7- Clone the Catalog application from Github:
Install git via sudo apt-get install git
Change directory via cd /var/www
Make a new directory via sudo mkdir catalog
Change ownership of the created catalog folder via sudo chown -R grader:grader catalog
Change directory via cd /catalog
Clone Item catalog project from github via git clone https://github.com/sarahbahgatelsawy/Udacity-Item-Catalog.git
Create a catalog.wsgi file, then put these lines into it
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0, "/var/www/catalog/")
from catalog import app as application
application.secret_key = 'add-secret-key'
Finally, rename run.py to init.py via mv run.py __init__.py
8- Install virtual environment for Python:
Install the virtual environment via sudo pip install virtualenv
Create a new virtual environment via sudo virtualenv venv
Activate the virtual environment source venv/bin/activate
Change permissions sudo chmod -R 777 venv
9- Install Flask and dependencies required:
Install pip via sudo apt-get install python-pip
Install Flask via pip install Flask
Install other dependencies via sudo pip3 install httplib2, sudo pip3 install requests, sudo pip3 install oauth2client, sudo pip3 install sqlalchemy, sudo pip3 install requests, sudo pip3 install psycopg2
10- Set up server configuration with apache:
Configure Apache Server by editing 000-default.conf file via sudo nano /etc/apache2/sites-enabled/000-default.conf, then add the following lines to 000-default.conf file
<VirtualHost *:80>
             ServerName http://3.9.175.130/
             WSGIScriptAlias / /var/www/catalog/catalog.wsgi
             <Directory /var/www/catalog/catalog/>
                     Order allow,deny
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
Reload and restart Apache Server via sudo service apache2 restart
Run python3 __init__.py, this will be shown  * Running on http://127.0.0.1:5000/
Then deactivate the virtual environment via deactivate.

11- References Used
1. SSH: How to Access a Remote Server and Edit Files https://www.youtube.com/watch?v=HcwK8IWc-a8
2. https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps
3. https://github.com/rrjoson/udacity-linux-server-configuration/blob/master/README.md
4. https://github.com/boisalai/udacity-linux-server-configuration
5. https://medium.com/@dushan14/create-a-web-application-with-python-flask-postgresql-and-deploy-on-heroku-243d548335cc
6. Deploying Flask App with Heroku https://www.youtube.com/watch?v=5UNAy4GzQ5E
7. https://www.codementor.io/abhishake/minimal-apache-configuration-for-deploying-a-flask-app-ubuntu-18-04-phu50a7ft

