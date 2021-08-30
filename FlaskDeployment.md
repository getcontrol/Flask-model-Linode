# Flask-on-Linode
If you are just learning how to use Python and have started dabbling with Web Applications that serve machine learning models,
then this guide may help you deploy your app!

The goal is to deploy our web application with as little configuration and server management as possible.

#### Tech Used
- [Python](https://www.python.org/)
- [Flask](https://flask.palletsprojects.com/en/1.0.x/)
- [NGINX](https://www.nginx.com/resources/wiki/)
- [Gunicorn](http://docs.gunicorn.org/en/stable/)
- Supervisor

## Table of Contents
- [Before You Begin](https://github.com/abalarin/Flask-on-Linode/blob/master/FlaskDeployment.md#before-you-begin)
- [Move your App to your Linode](https://github.com/abalarin/Flask-on-Linode/blob/master/FlaskDeployment.md#move-your-app-to-your-linode)
- [Configure Environment Variables](https://github.com/abalarin/Flask-on-Linode/blob/master/FlaskDeployment.md#configure-environment-variables)
- [Configure NGINX](https://github.com/abalarin/Flask-on-Linode/blob/master/FlaskDeployment.md#configure-nginx)
- [Install Python and Packages](https://github.com/abalarin/Flask-on-Linode/blob/master/FlaskDeployment.md#install-python-and-packages)
- [Deploy your Application](https://github.com/abalarin/Flask-on-Linode/blob/master/FlaskDeployment.md#deploy-your-application)
- [Configure Supervisor](https://github.com/abalarin/Flask-on-Linode/blob/master/FlaskDeployment.md#configure-supervisor)

## Before You Begin
You'll want to make sure you have already built out a Flask Application that you are ready to deploy to a production environment. If you haven't already created an app or its still under constructions you can use this [Simple Flask Blog Application](https://github.com/getcontrol/Flask-on-Linode/) for this tutorial.

We are also going to assume that you have already run through the [Getting Started with Linode](https://www.linode.com/docs/getting-started/) Guide and can SSH into your Linode.

You may want to source control your project with [git](https://github.com) so that later down the road you can implement Continuous Integration/Deployment development into your application.

## Move your App to your Linode
So you have finished developing your shiny Python Flask Application and want to deploy it to a production environment for the world to see. You can either retrieve your app from source control or SCP it from your Local Machine.

#### Retrieving your Application from source control
1. SSH into your Linode
```
ssh user@<Linode-IP>
```
2. Navigate to your Home directory
```
cd /home
```
3. Install your essential Linux packages to get started (enter command one by one):  
```
sudo apt update
sudo apt upgrade
sudo apt install git
```

3. Pull from source control (Replace my repo with yours)
```
git clone https://github.com/getcontrol/Flask-on-Linode && cd Flask-on-Linode
```

#### Retrieving your Application from your Local Machine
1. SCP your Applications root directory into your Linode's Home directory
```
scp -r flask_app/ user@<Linode-IP>:/home
```

## Configure [NGINX](https://www.nginx.com/)
NGINX is a free, open-source, high-performance HTTP server and reverse proxy, as well as an IMAP/POP3 proxy server.
1. Install NGINX
```
sudo apt install nginx
```
2. Create an NGINX Configuration file
```
sudo nano /etc/nginx/sites-enabled/flaskapp
```
```
server {
	listen 80;
	server_name <Your Linodes IP>;

	location / {
		proxy_pass http://127.0.0.1:8000;
		proxy_set_header Host $host;
		proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
	}
}
```
3. Unlink the NGINX default config
```
unlink /etc/nginx/sites-enabled/default
```
4. Reload your NGINX server
```
sudo nginx -s reload
```

###### If you try navigating to your Linode's IP in a web browser you should get the following or a similar error.
![NGINX Bad Gateway](https://us-east-1.linodeobjects.com/linodestuff/badgateway.png)
###### Next we are going to set up our Web Server Gateway Interface (WSGI) so that NGINX can communicate with our application and the internet can enjoy our stuff.

## Install Python and Packages
You should now be in your applications root directory on your Linode.
1. Install [Python 3](https://www.python.org/download/releases/3.0/)
```
sudo apt install python3
```
2. Install [pip](https://pip.pypa.io/en/stable/installing/)
```
sudo apt install python3-pip
python3 -m pip install --upgrade --force-reinstall pip
```
3. Install Flask Packages/libraries. If you are using the example [Flask Blog Application](https://github.com/abalarin/Flask-on-Linode) then the packages will be located in flask_app/requirements.txt
```
 pip install -r flask_app/requirements.txt
```

## Deploy your Application


1. Start the app
```
python3 app.py
```
2. Your app is running successfully if you see this!  
```
Transformation Pipeline and Model Sucessfully Loaded
 * Serving Flask app 'model' (lazy loading)
 * Environment: production
   WARNING: This is a development server. Do not use it in a production deployment.
   Use a production WSGI server instead.
 * Debug mode: on
 * Running on http://xx.xx.xx.xx:5000/ (Press CTRL+C to quit)
 * Restarting with stat
Transformation Pipeline and Model Sucessfully Loaded
 * Debugger is active!
 * Debugger PIN: 673-488-585
```
