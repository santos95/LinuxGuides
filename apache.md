## Install and set basic apache2 server in ubuntu
### Install the apache2 service
    sudo apt-get install apache2

### Check the ufw - If is enabled we have to allow apache in the firewall to allow outside access to the default web ports. During the instalation apache register itself in ufw, so it's provide some application profiles that can be used to enable/disable access to apache through the firewall.
    ufw status 
    ufw app list

### with the ufw app list, ufw display some able profiles, for apache 
Output
Available applications:
  Apache
  Apache Full
  Apache Secure

### Apache: basics config - only open the port 80 to allow http traffic (unencrypted web traffic). Apache Full: Opens the port 80 and the port 443 (encrypted and unencrypted web traffic). Apache Secure: This profile only open the port 443 (TLS/SSL encrypted web traffic)
    sudo ufw allow 'apache'
    sudo ufw status

### To check if is running successfully - in the web browser must prompt the apache default page:
    http://ip:8080
    http://hostname/

### Manage the Apache web server service - When we perform some configuration settings is not necessary to restart the service and dropping the connections, So we can make configuration changes without dropping the connections with systemctl reload apache2
    systemctl status apache2
    systemctl start/stop apache2
    systemctl restart apache2
    systemctl reload apache2

#### by default apache is configured to start automatically when the server boots. To disable the behavior
    systemctl disable apache2
#### To re-enabe or enable:
    systemctl enable apache2

### Setting a virtual host with apache:
#### Virtual hosts are used to encapsulate configuration details and hosts from more that one domain within a single server.  In apache from the 18.04 ubuntu version, has a default server block configured to serve documents from /var/www/html, which works from a single site, if we have more than one can be unwieldy. For that we can create a specific directory structure for our site in /var/www, leaving the default for cases in which the request does not match others sites.
    sudo mkdir /var/www/your_domain
    sudo mkdir /var/www/testdomain
    sudo chown -R $USER:$USER /var/www/your_domain
    sudo chmod -R 755 /var/www/your_domain

#### creates the sample index.html for our site:
     nano /var/www/testdomain/index.html
<html>
    <head>
        <title>Welcome to Your_domain!</title>
    </head>
    <body>
        <h1>Success!  The your_domain virtual host is working!</h1>
    </body>
</html>
