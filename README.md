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
1. Update all the installed packages 
```
apt-get update
apt-get upgrade
```
2. Change default SSH port from 22 to 2200
```
sudo nano /etc/ssh/sshd_config
```
Then change port 22 -> 2200

3. Configure the Firewall
```
ufw allow 2200/tcp
ufw allow 80/tcp
ufw allow 123/udp
ufw enable
```
Then check the firewall status ```ufw status```
