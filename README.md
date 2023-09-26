# Kittygram social network 
###### Dedicated to every kitty in the world

___

### INTRODUCTION
A social network for furry and cute cats!

___

### PROJECT DESCRIPTION

Welcome to Kittygram, a wonderful platform for all cat lovers! Get ready to create accounts, keep cat profiles in a live community of other cat lovers. Demonstrate the photogenic charm of your cat, share her most unusual achievements and celebrate birthdays in style. Study adorable feline profiles, and bask in the glory of all feline! Sign up today and embark on a journey through the charming world of our furry companions
___

### TECH

[Python] - backend target language

[Django Rest Framework] - Python toolkit for building Web APIs

[JavaScript] - frontend target language

[React] - JavaScript library for building user interfaces

[Ubuntu] - open source operating system based on Linux
 
[Gunicorn] - Python WSGI HTTP Server for UNIX

[Nginx] - HTTP and reverse proxy server

___

### DEPLOYMENT

Obtain a domain name

Set up a remote server with Ubuntu

##### **Perform the following steps on your remote server!**

Install python3

```
sudo apt install python3-pip python3-venv -y
```

Install npm

```
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash - &&\
sudo apt-get install -y nodejs
npm -v
# 9.5.0 (or newest)
```

Install Nginx

```
sudo apt install nginx -y
```

Clone the "Kittygram" repository and navigate to it

```
git clone https://git@github.com:AndreyTsay/infra.git
.git
cd infra
```

Create and activate a virtual environment

```
python3 -m venv venv
sourse venv/bin/activate
```

Update pip and install dependencies from requirements.txt

```
python -m pip install --upgrade pip
pip install -r requirements.txt
```

Create the .env file and fill up according the .env.excample file

```
touch .env
nano .env

# SECRET_KEY=<your_secret_key>
# DEBUG=<you_private_settings>
# ALLOWED_HOSTS=<you.ip>
```

Navigate to the backend folder and apply migrations

```
python manage.py migrate
```

Create an admin (superuser)

```
python manage.py createsuperuser
```

Navigate to the frontend folder and install dependencies for the front-end application:

```
cd ../frontend
npm i
```

Collect the frontend static

```
npm run build
```

Bind Gunicorn and the backend wia systemd

```
sudo nano /etc/systemd/system/gunicorn.service
```

Past the following code, replacing `<system-username>` 3 times with your actual data

Note: To find the exact path to Gunicorn, activate virtual environtment and use `which gunicorn` in command prompt

```
[Unit]
Description=gunicorn daemon
After=network.target

[Service]
User=<system-username>
WorkingDirectory=/home/<system-username>/infra/backend/
ExecStart=/home/<system-username>/infra/backend/venv/bin/gunicorn --bind 0.0.0.0:8000 kittygram_backend.wsgi

[Install]
WantedBy=multi-user.target
```

Start the Gunicorn proccess and add it to Ubunty startup

```
sudo systemctl start gunicorn
sudo systemctl enable gunicorn
```

Copy the frontend and backend static to the system default static folder, replacing `<system-username>` with your actual data

```
sudo cp -r /home/<system-username>/infra/frontend/build/. /var/www/kittygram/
sudo cp -r /home/<system-username>/infra/backend/static_backend/ /var/www/kittygram/
```

Create local folder to collect media and update rules, replacing `<system-username>` 3 times with your actual data: 

```
sudo mkdir media
sudo chown -R <system-username> /var/www/kittygram/media/
```

Open the Nginx config file for editing

```
sudo nano /etc/nginx/sites-enabled/default
```

Replace everything with the following code (replace `<you.ya.cloud.ip>` and `<youdomainname>` with you actial data)

```
server {

    listen 80;
    server_name <you.ip> <youdomainname>;

    location /api/ {
        proxy_pass http://127.0.0.1:8000;
    }

    location /admin/ {
        proxy_pass http://127.0.0.1:8000;
    }

    location /media/ {
        root /var/www/kittygram;
    }

    location / {
        root /var/www/Kittygram_Social_Network;
        index index.html index.htm;
        try_files $uri /index.html;
    }

}
```

Set up the Ubuntu firewall and start it
```
sudo ufw allow 'Nginx Full'
sudo ufw allow OpenSSH
sudo ufw enable
```

Run Nginx

```
sudo systemctl start nginx
```
___
