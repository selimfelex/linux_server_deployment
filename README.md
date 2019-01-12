# linux_server_deployment
deploy Linux server on the cloud serving python ,flask, WSGI, Apache, PostgerSQL

## Project Description
 this project will describe how to deploy Linux web server on Amazon web services AWS utilizing the technologies of Python SQLAlchemy, Flask Framework, OAuth 2.0 Protocol, APIs, Apache, WSGI and  PostgreSQL to run Python web-application  called (Item Catalog)

## Project_Specification
the main page of the application can be accessed by opening the URI http://18.217.208.41.xip.io 
the main page will contain two lists to  show all categories with recently added items  (main page)
- Clickable categories are listed in the navigation bar,
whenever a category is clicked showCategoryItems  function will be executed to view all items in that category
- Clickable list of recent items (last added items) are shown along with the category name
they belong to, whenever an item is clicked, Browse Item function will be executed
- JSON API is used to provide JSON format for items and thier categories and for a speciefic item

## Project_security
the pages are secured with google OAuth 2.0 API , so in order to be able to add ,delete ,update categories or items of you own you should login to the app with your google account . 


## Requirements
Virtual machin running Ubontu hosted in Amazon or Google cloud services
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
