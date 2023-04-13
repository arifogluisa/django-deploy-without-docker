# Deploy Django project with Nginx + Gunicorn (without docker)
Let's say we have a regular VPS with Ubuntu OS, and we have already written our Django website in PyCharm or in a notepad, and all that remains is to publish it, bind the domain, install the certificate and go.

## Step 1
We need to update the list of repositories and install the nginx package right away:
```shell
apt-get update
apt-get install nginx
```
##
## Step 2
I decided to clone my project to ```/var/www``` directory. So I go to ```/var/www``` with ```cd /var/www``` and clone my project here. Let's say my project's name is BlogProject. So I need to move to project folder with ```cd BlogProject``` . Now I supposed to be in this path ```/var/www/BlogProject``` . Now I create a virtual environment with ```python3 -m venv .venv``` and activate it with ```source .venv/bin/activate``` . Now I can install project's dependecies ```python3 -m pip install -r requirements.txt``` , just be sure there is "gunicorn" package in requirements.txt file.

##
## Step 3 - Gunicorn setup
Go to the ```/etc/systemd/system/``` and create gunicorn.service and gunicorn.socket files. 
##
gunicorn.service file:

```
[Unit]
Description=gunicorn daemon
Requires=gunicorn.socket
After=network.target

[Service]
User=root
WorkingDirectory=/var/www/BlogProject # path where manage.py file in.
ExecStart=/var/www/BlogProject/.venv/bin/gunicorn --workers 5 --bind unix:/run/gunicorn.sock BlogProject.wsgi:application
# admitted main app name is same as project name, so we use "BlogProject.wsgi:application" above.

[Install]
WantedBy=multi-user.target
```
##
gunicorn.socket file:

```
[Unit]
Description=gunicorn socket

[Socket]
ListenStream=/run/gunicorn.sock

[Install]
WantedBy=sockets.target
```
##

you can check gunicorn.service file for errors:

```
systemd-analyze verify gunicorn.service
```
##
## Step 4 - NGINX setup
Go to ```/etc/nginx/sites-available/``` and create a file without extension, I will name it ```default``` . You can name it with name of your project.

```
server {
    listen 80;
    server_name example.com;  # if you don't have domain yet, you can write your server IP here.
    
    location = /favicon.ico { access_log off; log_not_found off; }
    location /static/ {
        root /var/www/BlogProject;           # path to static folder
    }
    
    location /media/ {
        root /var/www/BlogProject;           # path to media folder
    }
    
    location / {
        include proxy_params;
        proxy_pass http://unix:/run/gunicorn.sock;
    }
}
```

##

to see logs, create a "log" folder in project directory, and create nginx-access.log and nginx-error.log files in log folder and use:

```
access_log /var/www/BlogProject/log/nginx-access.log;
error_log /var/www/BlogProject/log/nginx-error.log;
```

instead of:

```
location = /favicon.ico { access_log off; log_not_found off; }
```

##
In order to create a symbolic link to a file in the ```/etc/nginx/site-enabled/``` directory, enter the following command: 

```
sudo ln -s /etc/nginx/sites-available/default /etc/nginx/sites-enabled/
```
##
with any changes to the original file, run the command:

```
sudo systemctl restart nginx
```
##
## Last Step
To check the nginx configuration, you need to enter the command:
```
sudo nginx -t
```
##
start the gunicorn service and create a socket:
```
sudo systemctl enable gunicorn
sudo systemctl start gunicorn
```
##
To view the status of a running service, enter:
```
sudo systemctl status gunicorn
```
##
To check the creation of a socket, you must enter the command:
```
file /run/gunicorn.sock
```
this output is considered correct: /run/gunicorn.sock: socket
##
if you have made any changes to the gunicorn.service or .socket file, you must run the command:
```
systemctl daemon-reload
```
if everything worked fine, then we can start nginx:
```
sudo service nginx start
```
