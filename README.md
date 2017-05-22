# Configure-Linux-Server
## Project General Information
The complete URL for the project is <a href="http://52.15.168.179/">http://52.15.168.179</a></br>
The SSH port number: 2200</br>
The IP address is: 52.15.168.179 </br>

## Steps to configure the Linux Server
1. Remote login to AWS server(port number 22 -> 2200 after changing default SSH port)
```
ssh -i ~/.ssh/[SERVER KEY FILE] ubuntu@52.15.168.179 -p 22
```
2. Update all the installed packages 
```
sudo apt-get update
sudo apt-get upgrade
```
3. Change default SSH port from 22 to 2200
```
sudo nano /etc/ssh/sshd_config
```
Then change port 22 -> 2200

4. Configure the Firewall
```
sudo ufw allow 2200/tcp
sudo ufw allow 80/tcp
sudo ufw allow 123/udp
sudo ufw enable
```
Then check the firewall status ```sudo ufw status```

5. Install and Configure Postgresql
Install Postgresql ```sudo apt-get install postgresql```</br>
Check if no remote connections are allowed ```sudo nano /etc/postgresql/9.3/main/pg_hba.conf```</br>
Login as user "postgres" ```sudo su - postgres```</br>
Enter postgresql ```psql```</br>
Create new database, user and set permissions 
```
postgres=# CREATE DATABASE catalog;
postgres=# CREATE USER catalog;
postgres=# ALTER ROLE catalog WITH PASSWORD 'udacity';
postgres=# GRANT ALL PRIVILEGES ON DATABASE catalog TO catalog;
```
Then exit from psql.

6. Install git and set FlaskApp project
```
sudo apt-get install git
cd /var/www
mkdir FlaskApp
git clone https://github.com/fengliu414/car_catalog.git
mv ./car_catalog ./FlaskApp
cd FlaskApp
mv project.py __init__.py
```
7. Replace all ```engine = create_engine('sqlite:///cars_database.db')``` to 
```engine = create_engine('postgresql://catalog:udacity@localhost/catalog')
``` in __init__.py, database_setup.py, and lotsofcars.py files.

8. Install Flask, SQLAlchemy and modules that support OAuth authentication
```
sudo apt-get install python-psycopg2 python-flask
sudo apt-get install python-sqlalchemy python-pip
sudo pip install oauth2client
sudo pip install requests
sudo pip install httplib2
```
9. Install Apache web server and mod_wsgi application
```
sudo apt-get install apache2
sudo apt-get install libapache2-mod-wsgi
```
10. Configure .wsgi File
```
cd /var/www/FlaskApp
sudo nano FlaskApp.wsgi
```
then copy the content below into the file
```
#!/usr/bin/python
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0,"/var/www/catalog/")

from FlaskApp import app as application
application.secret_key = 'super_secret_key'
```
11. Configure and Enable a Virtual Host for FlaskApp project
```
sudo nano /etc/apache2/sites-available/FlaskApp.conf
```
then copy the content below into the file
```
<VirtualHost *:80>
    ServerName http://52.15.168.179/
    ServerAdmin fengliu414@gmail.com
    WSGIScriptAlias / /var/www/FlaskApp/FlaskApp.wsgi
    <Directory /var/www/FlaskApp/FlaskApp/>
        Order allow,deny
        Allow from all
    </Directory>
    Alias /static /var/www/FlaskApp/FlaskApp/static
    <Directory /var/www/FlaskApp/FlaskApp/static/>
        Order allow,deny
        Allow from all
    </Directory>
    ErrorLog ${APACHE_LOG_DIR}/error.log
    LogLevel warn
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
After editing the file, enable site ```sudo a2ensite FlaskApp```

12. Restart Apache Service
```
sudo service apache2 restart
```
Now you should check whether the URL <a href="http://52.15.168.179/">http://52.15.168.179</a> can be accessed
If not, check the error.log file for details
```
cd /var/log/apache2
nano error.log
```

13. Add another user that allows remote login to the ubuntu server
```
sudo adduser grader
touch /etc/sudoers.d/grader
sudo nano /etc/sudoers.d/grader
```
type in ```grader ALL=(ALL:ALL) ALL``` and save 

14. Generate KeyPair on local machine
Type ```ssh-keygen``` to generate KeyValuePair</br>
At the prompt to enter filename, type ```~/.ssh/graderKey```</br>
Copy the content in graderKey.pub

15. Save the public key to the server
Switch user as grader ```su - grader```. Note that you may need to enter password
```
mkdir .ssh
touch .ssh/authorized_keys
nano .ssh/authorized_keys
```
Then paste the content copied in step 14 into this file

16. Change the access permission to this public key
```
chmod 700 ~/.ssh
chmod 644 ~/.ssh/authorized_keys
```
Then reload the SSH ```service ssh restart```
Now user can remote login to the server as "grader"
```
ssh -i [privateKeyFilename] grader@52.15.168.179
```




