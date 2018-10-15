# FSND Linux Server Configuration
 a baseline installation of a Linux distribution on a virtual machine and prepare it to host your web applications, to include installing updates, securing it from a number of attack vectors and installing/configuring web and database servers
 
|   Name   	|                  Value                  	|
|:--------:	|:---------------------------------------:	|
| IP       	| 67.205.134.235                          	|
| SSH PORT 	| 2200                                    	|
| Username 	| grader (SSH key in "Notes to Reviewer") 	|
| URL      	| http://www.67.205.134.235.xip.io/       	|

## Update all currently installed packages
1. **Update all currently installed packages**
```
sudo apt-get update
sudo apt-get dist-upgrade
```
2. **Remove unnecessary packages**
```
sudo apt-get autoremove
```
# Change the SSH port from 22 to 2200
1. **change port 22 to 2200**
```
sudo nano /etc/ssh/sshd_config
```
2. **restart sshd service**
```
sudo service sshd restart
```

# Configure the Uncomplicated Firewall
1. **allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123).**
```
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow 2200/tcp
sudo ufw allow www
sudo ufw allow ntp
sudo ufw enable
```
# Create a new user named grader and grant this user sudo permissions
1. **Created a new user named grader**
```
sudo adduser grader
```
2. **add following text** `grader ALL=(ALL:ALL) ALL`
```
sudo nano /etc/sudoers.d/grader
```
3. **Configure keys on  local machine**
   * run ssh-keygen
```
ssh-keygen
```
 * Enter file in which to save the key
```
Enter file in which to save the key (C:\Users\user-/.ssh/id_rsa): 
```
* Enter passphrase (empty for no passphrase) & Enter same passphrase again
* copy **key info** content to paste into file at the server

4. **Configure keys on  the server**
   * Create a directory on the server to store keys: `mkdir .ssh`
   * Create a file to store keys: `touch .ssh/authorized_keys`
   * paste key content in authorized_keys 
   * Change the file permissions of the .ssh directory to 700 (this means only the file owner can read, write, or open the directory): `chmod 700 .ssh`
   * Change the file permissions of the authorized_keys file to 644 (this means only owner can write, others can read): `chmod 644 .ssh/authorized_keys`
 # Configure the local timezone to UTC.
  * `sudo timedatectl set-timezone Etc/UTC`
 # Install and configure Apache to serve a Python mod_wsgi application

* Install Apache `sudo apt-get install apache2`
* Install mod_wsgi `sudo apt-get install libapache2-mod-wsgi python-dev` & Enabke mod_wsgi `sudo a2enmod wsgi
* create a directory ` mkdir /var/www/catalog`
* Move inside this directory and clone project by git clone 
```
sudo git clone https://github.com/anexbmx/build-an-item-catalog-application.git
```
* rename catalog  project directory to  catalog `mv build-an-item-catalog-application catalog`
* move inside catalog and rename application.py to _ _init_ _.py `mv application.py to __init__.py`
`
* Install pip and necessary packages 
```
sudo apt-get install python-pip
sudo pip install virtualenv
sudo virtualenv venv
source venv/bin/activate 
sudo pip install Flask
sudo pip install sqlalchemy
sudo pip install python-psycopg2
sudo pip install oauth2client
sudo pip install requests
```
* Add the following to `sudo nano /etc/apache2/sites-available/Catalog.conf`
```
<VirtualHost *:80>
   ServerName 67.205.134.235
   ServerAdmin anexbm@67.205.134.235
   ServerAlias 67.205.134.235.xip.io
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
```

* Enable site `sudo a2ensite Catalog`
* create new file __catalog.wsgi__ inside __/var/www/catalog/__ 
* add the following code to __catalog.wsgi__
```
   #!/usr/bin/python
   import sys
   import logging
   logging.basicConfig(stream=sys.stderr)
   sys.path.insert(0,"/var/www/catalog/")

   from catalog import app as application
   application.secret_key = 'your secret key'
   ```
* `sudo service apache2 restart`

























