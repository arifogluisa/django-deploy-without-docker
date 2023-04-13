# Deploy Django project with Nginx + Gunicorn
Let's say we have a regular VPS with Ubuntu OS, and we have already written our Django website in PyCharm or in a notepad, and all that remains is to publish it, bind the domain, install the certificate and go.

## Step 1
We need to update the list of repositories and install the nginx package right away:
```shell
apt-get update
apt-get install nginx
```

## Step 2