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
apt-get update
apt-get upgrade
```
3. Change default SSH port from 22 to 2200
```
sudo nano /etc/ssh/sshd_config
```
Then change port 22 -> 2200

4. Configure the Firewall
```
ufw allow 2200/tcp
ufw allow 80/tcp
ufw allow 123/udp
ufw enable
```
Then check the firewall status ```ufw status```

5. Install and Configure Postgresql
Install Postgresql 
```sudo apt-get install postgresql
```
Check if no remote connections are allowed ```sudo nano /etc/postgresql/9.3/main/pg_hba.conf```
Login as user "postgres" ```sudo su - postgres```
Enter postgresql ```psql```
Create new database, user and set permissions 
```postgres=# CREATE DATABASE catalog;
   postgres=# CREATE USER catalog;
   postgres=# ALTER ROLE catalog WITH PASSWORD 'udacity';
   postgres=# GRANT ALL PRIVILEGES ON DATABASE catalog TO catalog;
```
Then exit from psql.

6. Install git and set FlaskApp project
```apt-get install git
cd /var/www
mkdir catalog
git clone https://github.com/vk9141/udacity-item-catalog.git
mv ./udacity-item-catalog ./catalog
cd catalog
mv project.py catalog.py
```
