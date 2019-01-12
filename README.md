# Linux Server Deployment
how to deploy you python web application and configure  Linux server on the cloud serving python ,flask, WSGI, Apache, PostgerSQL

## Project Description
 this project will describe how to deploy Linux web server on Amazon web services AWS utilizing the technologies of Python SQLAlchemy, Flask Framework, OAuth 2.0 Protocol, APIs, Apache, WSGI and  PostgreSQL to run Python web-application  called (Item Catalog)

## Project Specification
the main page of the application can be accessed by opening the URI http://18.217.208.41.xip.io 
the main page will contain two lists to  show all categories with recently added items  (main page)
- Clickable categories are listed in the navigation bar,
whenever a category is clicked showCategoryItems  function will be executed to view all items in that category
- Clickable list of recent items (last added items) are shown along with the category name
they belong to, whenever an item is clicked, Browse Item function will be executed
- JSON API is used to provide JSON format for items and thier categories and for a speciefic item

## Project security
the pages are secured with google OAuth 2.0 API , so in order to be able to add ,delete ,update categories or items of you own you should login to the app with your google account . 


## Requirements
Virtual machin running Ubntu hosted in Amazon or Google cloud services
Apache2 web server
PostgreSQL for Database 
Python 2.7.12
sqlalchemy
flask
json
oauth2client

## Linux installtion on Amazon lightsail
- create an account on Amazon https://aws.amazon.com/lightsail/
- afte that you should be able to login to your account consol
- select Launch a virtual machine with EC2 
- choose an Amazon Machine Image(AMI) to be Ubuntu Server from the free tiers group
- from the instance typy choose (general purpose)
- then click next with the default values untill you see your instance created in your EC2 Dashboard
- you should be able to download the Private key pair while you create the machine (very important)
-  after you download the private key put it in the .ssh folder and change its permission to 400
```
sudo chmod 400 AWSPrivateKeyPair.pem
```
* you should be able  to connect to the instance using ssh and the downloaded private key of the instance 
```
ssh -i "AWSPrivateKeyPair.pem" ubuntu@ec2-18-217-208-41.us-east-2.compute.amazonaws.com
```

* after you login to the machine make sure to update it to the latest version 
```
sudo apt-get update
sudo apt-get upgrade
```
### Securing access to your instance
we need to change the default ssh port and limit the open ports on the machine 
- Change the SSH port from 22 to 2200. Make sure to configure the Lightsail firewall to allow it
	* To change the default port the SSHD daemon is listening on. We can edit the configuration file using a nano editor:
	```
	$ sudo nano /etc/ssh/sshd_config
	```
	* Then look for the line below:
	```
	#Port 22
	```
	* First comment the line by removing the pound sign before the line. Then, enter your preferred port on the right side.
	```
	Port 2200
	```
	* To avoid completely locking yourself from your system, you should whitelist the port that you have specified above on your firewall, and in your Amazon console instance configuration  
	* go to your console and from networking and security select the security group
	from the action menu select Edit inbound rule , 
	make sure to add required ports 2200 tcp ,80 tcp, 123udp  and save the changes 

	* Once you make a change to the SSHD daemon configuration file, you should restart the services to reload the new changes.
	```
	$ sudo service sshd restart
	```
	* try to connect with the new ssh port 
	```
	ssh -i "AWSPrivateKeyPair.pem" ubuntu@ec2-18-217-208-41.us-east-2.compute.amazonaws.com -p 2200
	```
	* configure Uncomplicated firewall UFW to allow ports like
	```
	sudo ufw allow 2200/tcp
	sudo ufw allow 80/tcp 
	sudo ufw allow 123/udp 
	sudo ufw default allow outgoing
	sudo ufw default deny incoming
	sudo ufw enable 
	sudo service ssh restart
	```
- try to connect with the new ssh port 
	```
	ssh -i "AWSPrivateKeyPair.pem" ubuntu@ec2-18-217-208-41.us-east-2.compute.amazonaws.com -p 2200
	```

## Add a new user named grader and secure login with keypairs
- you can add user with the follwing command 
	```
	sudo adduser grader
	```
- giving sudo access 
	as users that has sudo access is included in the file insid folder called /etc.suders.d
	you can creat a new file or edit the already file created wit the instance name 
	you can find the file name with this command
	```
	sudo ls /etc/sudoers.d
	```
	you will see some file called 90-cloud-init-users
	hens you can edit this file and add entry for the new user grader to give him sudo access
	```
	sudo nano /etc/sudoers.d/90-cloud-init-users
	```

	as the file should look like this 
	```
	# Created by cloud-init v. 18.3-9-g2e62cb8a-0ubuntu1~18.04.2 on Mon, 07 Jan 2019 18:25:22 +0000
	# User rules for ubuntu
	ubuntu ALL=(ALL) NOPASSWD:ALL
	grader ALL=(ALL) NOPASSWD:ALL
	```
	save and exit the file 

- generating a new key pairs for the new user grader to allow login without password
  and then prohipt login with password to secure your server
  first Edit /etc/ssh/sshd-config  
	```
	sudo nano  etc/ssh/sshd-config
	```

	Make sure that you allow password access first to allow the grader user to login and create
	 .ssh folder and authorized_keys file to add the generated public key 
	 so the file should have these settings
	```	 
	PubkeyAuthentication yes
	RSAAuthentication yes
	PasswordAuthentication yes   #( this should be no after you finish)
	ChallengeResponseAuthentication yes  #( this should be no after you finish)
	```
	Then restart the ssh service with command
	```
	 sudo service ssh restart
	 ```
	- Then login with the grader user 
    - Create the .ssh foldr in your home directory
    ```
    mkdir .ssh
	```
    - Create the authorized_key file inside it
	```
    Touch .ssh/authorized_keys
	```

    Open your local terminal 
    Generate key pair(public and private using ssh-keygen command )
    Copy the content of your public key to the authorized_key file on the vm
	Put the private key under your .ssh folder in your windows local machine 
    Then try to login with
	```
    $ ssh -i "~/.ssh/grader_rsa" grader@ec2-18-217-208-41.us-east-2.compute.amazonaws.com -p 2200
    ```
	Then disable the following on /etc/ssh/ssh-config

	PasswordAuthentication no  

	ChallengeResponseAuthentication no

    Secure your ssh and authorized_key files with 
	```
 	chmod 700 .ssh
 	```
 	on our SSH directory, and
 	```
	chmod 644 .ssh/authorized_keys
	```
	on the authorized keys file.

## Install Apache and other software

- install the fowlling sofrware

	 ```
	 sudo timedatectl set-timezone UTC
	 sudo apt-get install apache2
	 sudo apt-get install python2.7
	 sudo apt-get install libapache2-mod-wsgi
	 sudo apt-get install postgresql
	 sudo apt-get install git
	 sudo apt-get install pip
	 sudo -H pip install SQLAlchemy
	 sudo -H pip install psycopg2 
	 sudo -H pip install flask
	 sudo -H pip install --upgrade google-api-python-client oauth2client
	 sudo -H pip install requests
	 ```

- configure Apache and WSGI to host your python web application 

	1. first you need to clone your app using git clone to the directory of /var/www/html
	```
	cd /var/www/html
	git clone https://github.com/selimfelex/FSND-ITEM-CATALOG-APP-PYTHON.git
	```
	 To serve the python application, it is important that Apache forward certain types of requests to mod_wsgi. It is also important to create a python file that tells mod_wsgi how to handle these requests.
	 You can do this by creating a website for WSGI that will tell Apache the location of python file and setup the file accordingly.
	 You then need to configure Apache to handle requests using the WSGI module. You’ll do this by editing the follwing file.
	 ```
	 sudo nano  /etc/apache2/sites-enabled/000-default.conf
	 ```
 	This file tells Apache how to respond to requests, where to find the files for a particular site and much more

	add the following lines inside virtualhost section 
	```
	WSGIScriptAlias / /var/www/application.wsgi
        <Directory /var/www/html/FSND-ITEM-CATALOG-APP-PYTHON>
        #WSGIProcessGroup OurApp
        #WSGIApplicationGroup %{GLOBAL}
        Order deny,allow
        Allow from all
        </Directory>
	```


	the first line tells apache how to handle requests to the / URL so it should be 
	forwarded to the application.wsgi that we will configur to point to our python app
	the second line tells where is the application and files located on the server 
	2. we need to create the wsgi file (application.wsgi)
	```
	touch /var/www/application.wsgi
	sudo nano /var/www/application.wsgi
	 ```
	then add the follwing setting under it 
	those lines tells the place of the application so that wsgi will import it as an object and handle its output to the appache to serve html requests
	```
	#!/usr/bin/python
	import os
	import sys
	import logging
	#activate_this = '/var/www/html/FSND-ITEM-CATALOG-APP-PYTHON/application.py'
	#execfile(activate_this, dict(__file__=activate_this))
	logging.basicConfig(stream=sys.stderr)
	sys.path.insert(0,"/var/www/html/FSND-ITEM-CATALOG-APP-PYTHON")
	from application import app as application
	```

	ps. in the last line (applicatin) word is the name of your main python file
	as it will import the app section from that file as an instance to run in the WSGI 

	3.  you need to add an empty file inside your app folder called __init__.py
	```	
	touch /var/www/html/FSND-ITEM-CATALOG-APP-PYTHON/__init.py
	```

## configure PostgreSQL

   On Linux systems, there is no default password set.
   To set the default password:
1. Connect to the default database with user postgres :

	```
	sudo -u postgres psql template1
	```

 2. Set the password for user postgres, then exit psql (Ctrl-D) :

	 ```	
     ALTER USER postgres with encrypted password 'xxxxxxx';
	 ```	

    Close psql. 
	 ```	
     \q
	 ```	

3. Edit the pg_hba.conf file :

	 ```	
	 sudo nano /etc/postgresql/10/main/pg_hba.conf
	 ```

	And change "peer" to "md5" on the line concerning postgres :

	```	
	local      all     postgres     peer md5
	# IPv4 local connections:
	host     all             catalog        127.0.0.1/32            md5
	```
	the last line will allow conection form local host only for a user catalog that we will ceate later to use it in our DB connection string 

4. open file named postgresql.conf
	```
	sudo nano /etc/postgresql/10/main/postgresql.conf
	```
	add the follwing ip to secure the connection to be from local host only
	```	
	# - Connection Settings -
	listen_addresses = '127.0.0.1'
	```

5. Restart the database :
sudo /etc/init.d/postgresql restart


6. create a database user named catalog and create database named catalog from the plsql 
	```
	CREATE DATABASE catalog ENCODING 'UTF8' ;
	create user catalog with password 'xxxxxx';
	ALTER USER catalog CREATEDB
	```


## how to un the app
make sure you run the fowlling files for the DB tables creation and  seed fron the app directiory
	```
	 cd /var/www/html/FSND-ITEM-CATALOG-APP-PYTHON/
	 python database_setup.py
	 python seeder.py
	```

## how to access the web app
by browsing the fowlling URI
	http://18.217.208.41.xip.io
	http://ec2-18-217-208-41.us-east-2.compute.amazonaws.com

