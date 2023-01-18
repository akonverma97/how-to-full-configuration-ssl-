# Full Configuration SSL Certificate in Ubuntu ec2 Instance
Firs Launch EC2 instance in aws account 
Ubuntu Server 18.04 LTS (HVM), SSD Volume Type - ami-0e472b###### (64-bit x86) / ami-0a940c###### (64-bit Arm)
Choose an Instance Type
- t2.nano
- t2.micro
- t2.large


whether you want
then click to 
- next :configure instance details


sclect to 
- IAM role


if you have IAM role slect your role 
- next ADD Storage
- next ADD Tags
- next configuration security group


configuration security group
you have already one port open 
- SSH 22

Now add rule select add rule 
- HTTP 8o 
- HTTPS 443

and Source open to the world 

Then click to review and launch

click to launch

Now you have a key slect your key and click to Launch instance
if you don't have a key 
- create a new key pair
- key pair name 


put your key name 
click to Launch instance
# now connected to ec2 instance
run command 
```
sudo su ubuntu
```
now  enter in your ec2 instance
# Now install to lapm server in your machine
now i am using this script for lamp server 
```
#!/bin/bash
sudo apt-get update
sudo apt-get -y install apache2
sudo apt-get -y install  php libapache2-mod-php php-mysql php-curl php-gd php-json php-zip php-mbstring
sudo service apache2 restart
sudo apt-get -y install mysql-server
sudo systemctl restart apache2 
```
complet lamp server install your ec2 instance


# Now point your Domain

go to route 53 
- slect Hosted zones


# select create record
- Record 

name your Domain 
- valu 

put value your instance ip

- TTL (seconds)

select to 1 minute

clike to sev 

# Now check to your Domain 
just type your domain in url 


Example

```
example.com
```

# Now we are install SSL certificate

run command 

```
sudo apt install certbot python3-certbot-apache
```

Confirm installation by pressing Y and then ENTER to accept.

# Step 2 — Checking Your Apache Virtual Host Configuration

```
sudo nano /etc/apache2/sites-available/your_domain.conf
```

Find the existing ServerName and ServerAlias lines:

```sh
...
ServerName your_domain;
SeverAlias www.your_domain
...
```

If your current virtual host configuration does not match up, then update it accordingly. After, save and exit the text editor. Then, validate your changes:

```
sudo apache2ctl configtest
```

With these changes in effect, Certbot will be able to find the correct VirtualHost block and update it.

# Step 3 — Allowing HTTPS Through the Firewall

Verify the type of traffic currently allowed on your server by running the following:

```
sudo ufw status
```

If you followed one of our Apache installation guide, your output will generate the following, this means that only HTTP traffic on port 80 is allowed:

```sh
Output
Status: active

To                         Action      From
--                         ------      ----
OpenSSH                    ALLOW       Anywhere                  
Apache                     ALLOW       Anywhere                  
OpenSSH (v6)               ALLOW       Anywhere (v6)             
Apache (v6)                ALLOW       Anywhere (v6)
```

To additionally let in HTTPS traffic, allow the Apache Full profile:

```
sudo ufw allow 'Apache Full'
```

Check the status 

```
sudo ufw status
```

You should receive the following output:

```sh
Output
Status: active

To                         Action      From
--                         ------      ----
OpenSSH                    ALLOW       Anywhere                  
Apache Full                ALLOW       Anywhere                  
OpenSSH (v6)               ALLOW       Anywhere (v6)             
Apache Full (v6)           ALLOW       Anywhere (v6)  
```

Now you’re ready to run Certbot and obtain your certificates.

# Step 4 — Obtaining an SSL Certificate

```
sudo certbot --apache
```

This command will generate a prompt with a series of questions to configure your SSL certificate. First, you’ll be asked to provide a valid email address, this is for the purposes of renewal notifications and security notices:

```sh
Output
Saving debug log to /var/log/letsencrypt/letsencrypt.log
Plugins selected: Authenticator apache, Installer apache
Enter email address (used for urgent renewal and security notices) (Enter 'c' to
cancel): you@your_domain
```

After you’ve provided a valid email address, press ENTER and proceed to the next step. You’ll be asked to confirm if you agree to Let’s Encrypt terms of service. Confirm by pressing A and ENTER:

```sh
Output
Please read the Terms of Service at
https://letsencrypt.org/documents/LE-SA-v1.2-November-15-2017.pdf. You must
agree in order to register with the ACME server at
https://acme-v02.api.letsencrypt.org/directory
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
(A)gree/(C)ancel: A
```

Next you’ll be asked if you would like to share your email with the Electronic Frontier Foundation to receive news and other informaiton. If you do not want to subscribe, press N, otherwise press Y and then ENTER to proceed to the next step:

```
Output
Would you be willing to share your email address with the Electronic Frontier
Foundation, a founding partner of the Let's Encrypt project and the non-profit
organization that develops Certbot? We'd like to send you email about our work
encrypting the web, EFF news, campaigns, and ways to support digital freedom.
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
(Y)es/(N)o: N
```

then click to enter

```sh
Output
Which names would you like to activate HTTPS for?
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
1: your_domain
2: your_domain
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Select the appropriate numbers separated by commas and/or spaces, or leave input
blank to select all options shown (Enter 'c' to cancel):
```

You’ll receive the following output:

```
Output
Obtaining a new certificate
Performing the following challenges:
http-01 challenge for your_domain
http-01 challenge for your_domain
Enabled Apache rewrite module
Waiting for verification...
Cleaning up challenges
Created an SSL vhost at /etc/apache2/sites-available/your_domain-le-ssl.conf
Enabled Apache socache_shmcb module
Enabled Apache ssl module
Deploying Certificate to VirtualHost /etc/apache2/sites-available/your_domain-le-ssl.conf
Enabling available site: /etc/apache2/sites-available/your_domain-le-ssl.conf
Deploying Certificate to VirtualHost /etc/apache2/sites-available/your_domain-le-ssl.conf
```

then download you key 


# Now Configuration SSL Certificate in local system

Configuring Apache to Use SSL

We have created our key and certificate files under the /etc/ssl directory. Now we just need to modify our Apache configuration to take advantage of these.

We will create a configuration snippet to specify strong default SSL settings.We will modify the included SSL Apache Virtual Host file to point to our generated SSL certificates.(Recommended) We will modify the unencrypted Virtual Host file to automatically redirect requests to the encrypted Virtual Host.

When we are finished, we should have a secure SSL configuration.


Modifying the Default Apache SSL Virtual Host File
Next, let’s modify /etc/apache2/sites-available/default-ssl.conf, the default Apache SSL Virtual Host file. If you are using a different server block file, substitute its name in the commands below.
Before we go any further, let’s back up the original SSL Virtual Host file:

```
sudo cp /etc/apache2/sites-available/default-ssl.conf /etc/apache2/
```

sites-available/default-ssl.conf.bak”
Now, open the SSL Virtual Host file to make adjustments:

```
sudo nano /etc/apache2/sites-available/default-ssl.conf”
```
Inside, with most of the comments removed, the Virtual Host file should look something like this by default:
```
/etc/apache2/sites-available/default-ssl.conf
```
```
<IfModule mod_ssl.c>
<VirtualHost _default_:443>
ServerAdmin webmaster@localhost

DocumentRoot /var/www/html

ErrorLog ${APACHE_LOG_DIR}/error.log
CustomLog ${APACHE_LOG_DIR}/access.log combined

SSLEngine on

SSLCertificateFile      /etc/ssl/certs/ssl-cert-fullchain.pem
SSLCertificateKeyFile /etc/ssl/private/ssl-cert-private.key

<FilesMatch "\.(cgi|shtml|phtml|php)$">
SSLOptions +StdEnvVars
</FilesMatch>
<Directory /usr/lib/cgi-bin>
SSLOptions +StdEnvVars
</Directory>

</VirtualHost>
</IfModule>
```

We will be making some minor adjustments to the file. We will set the normal things we’d want to adjust in a Virtual Host file (ServerAdmin email address, ServerName, etc., and adjust the SSL directives to point to our certificate and key files.

After making these changes, your server block should look similar to this:
```
/etc/apache2/sites-available/default-ssl.conf
```

```
<IfModule mod_ssl.c>
        <VirtualHost _default_:443>
ServerAdmin your_email@example.com
ServerName dev.test.com

DocumentRoot /var/www/html

ErrorLog ${APACHE_LOG_DIR}/error.log
CustomLog ${APACHE_LOG_DIR}/access.log combined

SSLEngine on

SSLCertificateFile      /etc/ssl/certs/apache-selfsigned.crt
SSLCertificateKeyFile /etc/ssl/private/apache-selfsigned.key
<
FilesMatch "\.(cgi|shtml|phtml|php)$">
SSLOptions +StdEnvVars
</FilesMatch>
<Directory /usr/lib/cgi-bin>
SSLOptions +StdEnvVars
</Directory>

</VirtualHost>
</IfModule>
```

Save and close the file when you are finished.


To create a symlink at current directory https.conf
```
	 ln -s /etc/apache2/sites-enable default-ssl.conf /etc/apache2/sites-enable/default-ssl.conf
```

```
<IfModule mod_ssl.c>
<VirtualHost *:443>
ServerAdmin webmaster@localhost
ServerName  dev.test.com
DocumentRoot /var/www/html
ErrorLog ${APACHE_LOG_DIR}/error.log
CustomLog ${APACHE_LOG_DIR}/access.log combined
<Directory /var/www/html>
Options -Indexes
AllowOverride All
</Directory>
<IfModule mod_dir.c>
DirectoryIndex index.php index.html index.cgi index.pl  index.xhtml index.html
</IfModule>
Include /etc/letsencrypt/options-ssl-apache.conf
SSLEngine on
SSLCertificateFile /home/bitcot/Downloads/fullchain.pem
SSLCertificateKeyFile /home/bitcot/Downloads/privkey.pem
</VirtualHost>
</IfModule>
```



now add the point of domain in local host
    			
```
sudo nano /etc/hosts
```



 Adjusting the Firewall

If you have the ufw firewall enabled, as recommended by the prerequisite guides, you might need to adjust the settings to allow for SSL traffic. Luckily, Apache registers a few profiles with ufw upon installation.

We can see the available profiles by typing:

                    
```                 		
sudo ufw app list
```
You should see a list like this:   

```
Output
Available applications:
  Apache
  Apache Full
  Apache Secure
  OpenSSH
```

You can see the current setting by typing:

```
sudo ufw status
```

If you allowed only regular HTTP traffic earlier, your output might look like this:

Output
Status: active
```
To                         Action      From
--                         ------      ----
OpenSSH                    ALLOW       Anywhere
Apache                     ALLOW       Anywhere
OpenSSH (v6)               ALLOW       Anywhere (v6)
Apache (v6)                ALLOW       Anywhere (v6)
```
```
UFW status inactive
```

```
sudo ufw enable	
```
then 
 		                             
```
sudo ufw status
```

```
Output
Status: active

To                         Action      From
--                         ------      ----
OpenSSH                    ALLOW       Anywhere
Apache                     ALLOW       Anywhere
OpenSSH (v6)               ALLOW       Anywhere (v6)
Apache (v6)                ALLOW       Anywhere (v6)
```



To additionally let in HTTPS traffic, we can allow the “Apache Full” profile and then delete the redundant “Apache” profile allowance:

```      
sudo ufw allow 'Apache Full'
```
```
sudo ufw delete allow 'Apache'
```
Your status should look like this now:

```
sudo ufw status
```
```
Output
Status: active

To                         Action      From
--                         ------      ----
OpenSSH                    ALLOW       Anywhere
Apache Full                ALLOW       Anywhere
OpenSSH (v6)               ALLOW       Anywhere (v6)
Apache Full (v6)           ALLOW       Anywhere (v6)
```


Enabling the Changes in Apache
We can enable mod_ssl, the Apache SSL module, and mod_headers, needed by some of the settings in our SSL snippet, with the a2enmod command:

```
sudo a2enmod ssl
```


Next, we can enable our SSL Virtual Host with the a2ensite command:

```
sudo a2ensite default-ssl
```

```
sudo apache2ctl configtest
```
If everything is successful, you will get a result that looks like this:

```
Output
AH00558: apache2: Could not reliably determine the server's fully qualified domain name, using 127.0.1.1. Set the 'ServerName' directive globally to suppress this message
Syntax OK
```

If your output has Syntax OK in it, your configuration file has no syntax errors. We can safely restart Apache to implement our changes:

```
sudo systemctl restart apache2
```

Testing Encryption

```
https://dev.test.com
```

Conclusion
You have configured your Apache server to use strong encryption for client connections. This will allow you serve requests securely, and will prevent outside parties from reading your traffic.

