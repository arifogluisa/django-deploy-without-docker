# Deploy Django project with Nginx + Gunicorn
Let's say we have a regular VPS with Ubuntu OS, and we have already written our Django website in PyCharm or in a notepad, and all that remains is to publish it, bind the domain, install the certificate and go.

## Step 1
We need to update the list of repositories and install the nginx package right away:
```shell
apt-get update
apt-get install nginx
```

## Step 2
I want to clone my project to "/var/www" directory. So I move to /var/www with ```cd /var/www``` and clone my project here. Let's say my project's name is
BlogProject. So I need to move to project folder with ```cd BlogProject``` . Now I supposed to be in this path ```/var/www/BlogProject``` . Now I create
virtual environment with ```python3 -m venv .venv``` and activate it with ```source .venv/bin/activate``` .

## Step 3
