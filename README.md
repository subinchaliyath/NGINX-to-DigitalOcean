# NGINX_Pro


 Node.js Deployment

> Steps to deploy a Node.js app to DigitalOcean using PM2, NGINX as a reverse proxy and an SSL from LetsEncrypt

## 1. Sign up for Digital Ocean
[ Digital Ocean](https://www.digitalocean.com/)

## 2. Create a droplet and log in via ssh
```
ssh root@ip
sudo apt update
adduser subin
usermod -aG sudo subin
#### firewall
ufw allow OpenSSH
ufw enable 
ufw status
rsync --archive --chown=subin:subin ~/.ssh /home/subin
```
## 3. Login as user and installing NGINX
```
ssh subin@ip
sudo apt update
sudo apt install nginx
sudo ufw app list
sudo ufw allow 'Nginx HTTP'
sudo ufw status
```

## 3. Pointing Domain to static file
```
sudo mkdir -p /var/www/subinchaliyath.me/html
sudo chown -R $USER:$USER /var/www/subinchaliyath.me/html
sudo chmod -R 755 /var/www/subinchaliyath.me
cd /var/www/subinchaliyath.me/html
```
clone your project (index. html should be here

```
sudo nano /etc/nginx/sites-available/subinchaliyath.me
```
```
server {
        listen 80;
        listen [::]:80;

        root /var/www/subinchaliyath.me/html/foldername;
        index index.html index.htm index.nginx-debian.html;

        server_name subinchaliyath.me www.subinchaliyath.me;

        location / {
                try_files $uri $uri/ =404;
        }
}
```
```
sudo ln -s /etc/nginx/sites-available/your_domain /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl restart nginx
```

## 4. Add SSL with LetsEncrypt

```
sudo apt install certbot python3-certbot-nginx
sudo ufw status
sudo ufw allow 'Nginx Full'
sudo ufw delete allow 'Nginx HTTP'
sudo certbot --nginx -d subinchaliyath.me-d www.subinchaliyath.me
sudo certbot renew --dry-run

```




## 5. Install Node/NPM
```
cd ~
curl -sL https://deb.nodesource.com/setup_14.x -o nodesource_setup.sh
sudo bash nodesource_setup.sh
sudo apt install nodejs
node -v

```

## 6. Clone your project from Github
There are a few ways to get your files on to the server, I would suggest using Git
```
git clone yourproject.git
```

### 7. Install dependencies and test app
```
cd yourproject
npm install
npm start (or whatever your start command)
```

```
sudo nano /etc/nginx/sites-available/subinchaliyath.me
```
## 9. Setup PM2 process manager to keep your app running
```
sudo npm install pm2@latest -g
pm2 start app (or whatever your file name)
# To make sure app starts when reboot
pm2 startup ubuntu
```
```
server {
...
    location /subin {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
...
}

````
```
sudo nginx -t
sudo systemctl restart nginx

````
[reference](https://www.digitalocean.com/community/tutorials/how-to-set-up-a-node-js-application-for-production-on-ubuntu-20-04)



