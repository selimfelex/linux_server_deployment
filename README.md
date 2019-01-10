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


