# Deploy Django project with Nginx + Gunicorn
Let's say we have a regular VPS with Ubuntu OS, and we have already written our Django website in PyCharm or in a notepad, and all that remains is to publish it, bind the domain, install the certificate and go.

## Step 1
We need to update the list of repositories and install the nginx package right away:
```shell
apt-get update
apt-get install nginx
```

## Step 2
I decided to clone my project to ```/var/www``` directory. So I go to ```/var/www``` with ```cd /var/www``` and clone my project here. Let's say my project's name is BlogProject. So I need to move to project folder with ```cd BlogProject``` . Now I supposed to be in this path ```/var/www/BlogProject``` . Now I create a virtual environment with ```python3 -m venv .venv``` and activate it with ```source .venv/bin/activate``` . Now I can install project's dependecies ```python3 -m pip install -r requirements.txt``` , just be sure there is "gunicorn" package in requirements.txt file.

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

## Step 4 - NGINX setup
