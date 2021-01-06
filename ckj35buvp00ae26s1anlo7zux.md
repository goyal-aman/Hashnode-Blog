## Deploy Django Applications on Linux server

# Introduction
Django is a Python powered web framework that can help you get you to build powerful web applications and APIs.

In this article, I'm going to guide you through how to deploy a Django web application on any Linux server. 

# Prerequisites
Before we go ahead I am assuming you are already familiar with basic stuff like virtual environments, and already have a Django project setup. Moving forward we will download the rest of the dependencies and packages.

# Configuration and Setup
we will start by preparing our Linux environment and virtual environment. Starting from installing *pip* python package manager.

```
sudo apt-get update
sudo apt-get install python3-pip
``` 
In this guide, we are going to be using python 3.8, with python venv module to handle our virtual environments.

```
sudo apt-get install python3-venv
```

If you don't already have a virtual environment set up, create and activate your virtual env as follows
```
python3 -m venv venv
source venv/bin/activate
```

Next, we need to set a directory that will hold out the site's static assets. this will allow Nginx to serve our static assets directly, thus improving performance. It is important to tell Django where are to look for these assets which will be our project's base directory. Add the below line to your projects settings.py
```
STATIC_ROOT = os.path.join(BASE_DIR, "static/")
```

Here,*BASE_DIR* is the path of our project's base directory and *static* is the directory to store our static assets.

Save the file. Now we need to collect all our static assets that you might have used in the project to this directory. Fortunately, Django provides us with a single command to achieve this.
```
python manage.py collectstatic
```
If asked type "yes" to confirm the action. Now is the right time to check if everything is working as intended. So, fire up the server and run tests if any.

```
python manage.py runserver
```
if you have unit test setup then,
```
python manage.py test
```

# Setting uWSGI Server
Django development server nice during development but when using our application in real-world we need to have something more reliable and secure. Hence **uWSGI Server**. In order to use uWSGI in our system, we need some dependencies and development files.
```
sudo apt-get install python-dev
pip3 install uwsgi
```
we can quickly check our server by passing information of our application. 
```
uwsgi -http :8080 --home /home/user/venv --chdir /home/user/myproject -w myproject.wsgi
```
change **/home/user/venv** with the path of your virtual environment, **/home/user/myproject** with the path of your project, and **myproject.wsgi** with your project's wsgi file.

what's going on here you ask? here we have told uwsgi to first change to our home directory then use our virtual environment for anything ahead. Change to our project directory and use our project uwsgi python file.

When you run the service and goto **/admin** of your project, you will notice though the functionality is working but CSS is not. In order to do so, we need to create our custom configuration files.

Start by creating a directory to store your configuration files.
```
sudo mkdir  -p /etc/uwsgi/sites
cd /etc/uwsgi/sites
```
in the **sites** directory we will place our configuration files. Create a **myproject.ini** configuration file
```
sudo nano myproject.ini
```
in the myprojct.ini files we will add the following content
```
[uwsgi]
project = myproject
base = /home/user

chdir = %(base)/%(project)
home = %(base)/venv
module = %(project).wsgi:application

master = true
processes = 5

socket = %(base)/%(project)/%(project).sock
chmod-socket = 664
vacuum = true
```
here, **myproject** needs to replaced with your project name, **/home/user** with path of your project's path. With this our configuration is almost complete. I'm not getting in deep about what this all really means for now. But provide enough reference at the end if you are intrested. Though we now have a configuration to serve our application but we haven't really automated the process to restart the server once we reboot the server. Thus we need an upstart script.

```
sudo nano /etc/init/uwsgi.conf
```
In the editor enter the following
```
description "uWSGI application server in Emperor mode"

start on runlevel [2345]
stop on runlevel [!2345]

setuid user
setgid www-data

exec /usr/local/bin/uwsgi --emperor /etc/uwsgi/sites
```

# Install and configure Nginx as a Reverse Proxy
With uWSGI configured and ready to go, we can now install and configure Nginx as our reverse proxy. This can be downloaded from Ubuntu’s default repositories:
```
sudo apt-get install nginx
```
now create a server block configuration file:
```
sudo nano /etc/nginx/sites-available/myproject
```
we can start our server block by indicating the port number and IP address where our first project should be accessible.

```
server {
    listen 80;
    server_name 123.456.789 456.789.123;
}
location /static/ {
        root /home/user/myproject;
}
location / {
        include         uwsgi_params;
        uwsgi_pass      unix:/home/user/myproject/myproject.sock;
    }
}

```
save and close the file when you are finished. Now we need to link our new configuration files to **Nginx's** **sites-enabled** directory to enable them.
```
sudo ln -s /etc/nginx/sites-available/myproject /etc/nginx/sites-enabled
```
check the syntax by typing
```
sudo service nginx configtest
```
If no syntax errors are detected, you can restart your nginx service to load the new configuration.
```
sudo service nginx start
```

You should now be able to reach your project by going to IP address. With everything working fine.