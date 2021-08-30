# Build and deploy your first machine learning web app to Linode
A beginner’s guide to train and deploy machine learning pipelines in Python using PyCaret

Inspired by these articles and Github repos:  
- [How to Move Machine Learning Model to Production Linode](https://www.linode.com/docs/guides/how-to-move-machine-learning-model-to-production)  
- [https://github.com/abalarin/Flask-on-Linode](https://github.com/abalarin/Flask-on-Linode)  
- [Build and Deploy Your First Machine Learning Model](https://www.kdnuggets.com/2020/05/build-deploy-machine-learning-web-app.html)  
- [https://github.com/pycaret/deployment-heroku](https://github.com/pycaret/deployment-heroku)    

## Application Breakdown
This is the breakdown of a fairly simple Flask Application serving a PyCaret pickled model.
```
Flask-on-Linode
│   README.md
│   FlaskDeployment.md
│   .gitignore
└───flask_app
│   │   app.py
│   │   deploy_28082021.pkl
│   │   requirements.txt
│   └───static
│   │   │   style.css
│   └───templates
│   │   │   home.html

```

## Deploying Locally
Lets walk through setting up your development environment and deploying this application on your local machine

1. Install Python, pip, and virtualenv
  - [Python](https://www.python.org/)
  - [pip](https://pip.pypa.io/en/stable/installing/)
  - [Virtualenv](https://virtualenv.pypa.io/en/latest/installation/)

2. Clone this repo and CD into the projects directory
```
git clone https://github.com/getcontrol/Flask-model-Linode  
cd flask_app  
```
3. Create and activate a virtualenv
```
virtualenv venv
source venv/bin/activate
```
4. Install packages
```
pip install -r flask_app/requirements.txt
```
5. Create Flask environment variables
```
export FLASK_APP=flask_app/app.py
export FLASK_ENV=development
```
6. Run it
```
flask run
```
## Move your App to your Linode

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
git clone https://github.com/getcontrol/Flask-model-Linode && cd Flask-model-Linode
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

## Install Python and Packages
You should now be in your applications root directory on your Linode.
1. Install [Python 3](https://www.python.org/download/releases/3.0/)
```
sudo apt install python3
```
2. Install [pip](https://pip.pypa.io/en/stable/installing/) and make sure its the latest version
```
sudo apt install python3-pip
python3 -m pip install --upgrade --force-reinstall pip
```
3. Install Flask Packages/libraries.
```
cd flask_app && pip install -r requirements.txt
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

## Custom Model Training

You can train your own Pycret model using the [Notebook](https://github.com/getcontrol/Flask-model-Linode/blob/main/flask_app/Insurance%20-%20Model%20Training%20Notebook.ipynb):

1.  Train the model and save it with a unique name "mymodel.pkl"

2. Change line 9 (remove file extension) :
```
model = load_model('mymodel')
```

3. Start the app
```
python3 app.py
```
