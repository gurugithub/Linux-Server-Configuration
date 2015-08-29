# Guru Shetti Aug 28th, 2015
# Guru Shetti Aug 29th, 2015 - Reformatted and added lines of code/commands based on feedback from Reviewer
# Udacity Project - Linux Server Configuration
-------------------------------------------------

This readme explains steps to host Item Catalog Application from a previous Udacity project (P3: Item Catalog)
Please refer to the P3 project submitted https://github.com/gurugithub/catalog.git for more info on the same

This file is from GitHub repo called Linux-Server-Configuration
# License Free to use
-------------------------------------------------

#Grading guidance for rubrics:

User Management: root use was disabled. A remote user was created that has sudo privilages. Its called "grader". Also password based access was removed allowing only key based login.

Security: Key based SSH auth is enforced and the SSH port is set to 2200. As mentioned below system updates were checked and applied.

# IP Address of the server application
-------------------------------------------------

54.213.0.20

# The IP address and SSH port so your server can be accessed by the reviewer.
-------------------------------------------------

The port number is set to 2200 as required by the project

# The complete URL to your hosted web application.
-------------------------------------------------

http://54.213.0.20/

# A summary of software you installed and configuration changes made.
-------------------------------------------------

1. fail2ban was installed which will monitor for unsuccessful login attempts. Defaults entries werre used otherwise it was noted in the config file

$sudo apt-get install fail2ban
$sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
$sudo nano /etc/fail2ban/jail.local (for localizing)

2. sendmail was installed and configured.

$sudo apt-get install sendmail iptables-persistent
$sudo service fail2ban start



3. "unattended-upgrades" was installed and enabled
4. SSH port was changed from 22 to 2200

$ nano /etc/ssh/sshd_config (PermitRootLogin no, PasswordAuthentication  no)
$sudo service sshd restart

5. UFW now allows only 2200, 80 and 123

$sudo ufw allow 2200/tcp
$sudo ufw allow 80/tcp
$sudo ufw allow 123/udp
$sudo ufw enable


6. Time zone was changed to UTC

$ sudo dpkg-reconfigure tzdata (none of above, select UTC)

7. python was already installed
8. Apache was installed and configured

$ sudo apt-get install apache2


$ ssh-keygen
Copy the public id to the server:
$ ssh-copy-id grader@ip -2200 

$ ssh -v grader@ip  -p2200




9. ssh-keygen was used to generate public and private keys. Public key was uploaded to the server for user "grader"

10. PasswordAuthentication back from yes to no

11. hosts file was updated with ip address

12. mod_wsgi was installed for serving Python apps from Apache and the helper package python-setuptools

$ sudo apt-get install python-setuptools libapache2-mod-wsgi

13. Each time config was changed for Apache, it was restarted

14. git was installed and configured

$ sudo apt-get install git

$ git config --global user.name "YOUR NAME"

$ git config --global user.email "YOUR EMAIL ADDRESS"


15. tested with small pyhton program to test apache and mod_wsgi - Everything looked great

$ sudo apt-get install libapache2-mod-wsgi python-dev

16. Installed pip for installing additional packages
$ sudo apt-get install python-pip
$ sudo virtualenv catvenv
$ sudo chmod -R 777 venv
$ source venv/bin/activate

17. Installed virtual enviromnet and an enviroment called "catvenv"

$ sudo pip install virtualenv


18. "catalog application" code from github was cloned into server from link https://github.com/gurugithub/catalog.git
19. The above application used sqlite for its databaaase. For the project requirement the project files were updated locally (rather not good but saved time for project submission)
20. application.py from "git cloned" was copied over to __init__.py
21. All permissions were given to the virtual environment and activated
22. using pip installed Flask

$ pip install Flask


23. installed flask_wtf. Please note I got errors when I installed in the virtual env. I have to install globally under grader user id and then the application ran

$ sudo pip install flask_wtf 

24. Paths in the __init__.py file were updated for the secret key from developer.google.com and developer.facebook.com
25. New virtual host was created for apache to reflect the code repo for catalog
26. virtual host was enabled

Create a virtual host config file
$ sudo nano /etc/apache2/sites-available/catalog.conf
Paste in the following lines of code and change names and addresses regarding your application:
  <VirtualHost *:80>
      ServerName PUBLIC-IP-ADDRESS
      ServerAdmin admin@PUBLIC-IP-ADDRESS
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
  
  
27. catalog.wsgi was created and app secret key was updated
28. apache was restarted
29. Installed httplib2 in virtual environment catvenv
30. Installed requests in virtual environment catvenv
31. Installed sqlalchemy in virtual environment catvenv
32. Installed oauth2client 
33. Installed postgresql

$ sudo apt-get install postgresql postgresql-contrib

$ sudo nano /etc/postgresql/9.3/main/pg_hba.conf
$ sudo nano database_setup.py
python engine = create_engine('postgresql://catalog:PW-FOR-DB@localhost/catalog')
$ mv application.py __init__.py

33.1 created user called catalog without sudo access
34. Configured to allow only local access for the database (actually that was default). 
35. alter access so that it can also create db
36. added user called catalog and removed super admin access. It still can create tables etc under its own role

$ sudo adduser catalog


37. Created DB for catalog tables (using script app_database_setup.py) under user catalog
38. Ran lotsofitems.py to load sampel data into tables
39. Updated applications on developer.google and developer.facebook to reflect the new URL and IP address so that oauth can run
40. Test application - ok to login using g+ and facebook
41. Installed "applicationinsights" for automated application status visualized in azure. This can be tailored to meet any automated security and application alert

# For automated application status (for exceeds expectaions) sample below
-------------------------------------------------

from flask import Flask
from applicationinsights.requests import WSGIApplication

*instantiate the Flask application and wrap its WSGI application
app = Flask(__name__)
app.wsgi_app = WSGIApplication('Guru azure key', app.wsgi_app)

*define a simple route
@app.route('/')
def hello_udacity():
    return 'Catalog App is running!'

* run the application
if __name__ == '__main__':
    app.run()

*****************end of auto status****************
********************************End of software and config*****************

# Comments are provided in config files
-------------------------------------------------

Before any steps the system was checked for updates and upgrades applied to the stock system



#A list of any third-party resources you made use of to complete this project.
-------------------------------------------------

1. http://serverfault.com/questions/110154/whats-the-default-superuser-username-password-for-postgres-after-a-new-install
2. http://www.postgresql.org/docs/9.1/static/app-createuser.html
3. https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps
4. http://blog.udacity.com/2015/03/step-by-step-guide-install-lamp-linux-apache-mysql-python-ubuntu.html
5. https://code.google.com/p/modwsgi/wiki/InstallationOnLinux
6. https://www.digitalocean.com/community/tutorials/how-to-install-and-use-postgresql-on-ubuntu-12-04
7. http://stackoverflow.com/questions/11583714/install-psycopg2-on-ubuntu
8. https://www.digitalocean.com/community/tutorials/how-to-install-and-use-postgresql-on-ubuntu-14-04
9. https://wiki.postgresql.org/wiki/Psycopg2_Tutorial
10. http://askubuntu.com/questions/154431/how-do-i-delete-multiple-lines-in-nano-without-affecting-the-clipboard
11. https://help.ubuntu.com/community/SSH/OpenSSH/Configuring
12. http://askubuntu.com/questions/342132/how-to-give-specific-port-to-apache-in-ubuntu-12-04-and-port-for-mysql
13. http://manpages.ubuntu.com/manpages/dapper/man1/cp.1.html
14. http://stackoverflow.com/questions/15648814/sqlalchemy-exc-argumenterror-cant-load-plugin-sqlalchemy-dialectsdriver
15. https://virtualenv.pypa.io/en/latest/
16. https://flask-wtf.readthedocs.org/en/latest/
17 https://www.digitalocean.com/community/tutorials/how-to-protect-ssh-with-fail2ban-on-ubuntu-14-04
18. https://github.com/Microsoft/ApplicationInsights-Python

Open your ~/.ssh/udacity_key.rsa file in a text editor and copy the contents of that file.

During the submission process, paste the contents of the udacity_key.rsa file into the "Notes to Reviewer" field.

# Please note above is posted during submission online. 