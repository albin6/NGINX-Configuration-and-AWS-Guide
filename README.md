# NGINX-Configuration-and-AWS-Guide

## 1. Create Free AWS Account
Create free AWS Account at https://aws.amazon.com/

## 2. Create and Lauch an EC2 instance and SSH into machine

### Configure Security Group in AWS EC2

1. **Navigate to EC2**:
   - Go to the **Services** menu and select **EC2**.

2. **Access Security Groups**:
   - In the left sidebar, under **Network & Security**, click on **Security Groups**.

3. **Create or Select a Security Group**:
   - Click on the **Create security group** button to create a new security group or select an existing one.

4. **Configure Inbound Rules**:
   - Click on **Inbound rules** and then **Edit inbound rules**.

5. **Add Rules for Allowed Traffic**:
   - Add rules for the types of traffic you want to allow:
     - **SSH (port 22)** for SSH access(if not exists in the rule).
     - **HTTP (port 80)** for web traffic.
     - **HTTPS (port 443)** for secure web traffic.
     - **Custom Type (give the port number that your project is running)**
6. **Choose Source for Each Rule**:
   - For each rule, select the appropriate **Source**:
     - You can allow traffic from specific IP addresses.
     - Alternatively, you can allow access from anywhere (`0.0.0.0/0`), though this is less secure.

7. **Save the security group and assign it to your EC2 instance**:

## 3. Install Node and NPM
```
sudo apt update

sudo apt install curl

curl -fsSL https://deb.nodesource.com/setup_lts.x | sudo -E bash -

sudo apt install -y nodejs

node -v

npm -v

```

## 4. Clone your project from Github
```
git clone https://github.com/<github-username>/<your-repository-name>.git
```

## 5. Install dependencies
```
sudo npm i pm2 -g
pm2 start index

# To make sure app starts when reboot
pm2 startup ubuntu
```

## 6. Setup Firewall
```
sudo ufw enable
sudo ufw status
sudo ufw allow ssh (Port 22)
sudo ufw allow http (Port 80)
sudo ufw allow https (Port 443)
```

## 7. Install NGINX and configure
```
sudo apt install nginx

sudo nano /etc/nginx/sites-available/default
```
Not recommended to edit the default file you can create dedicated file for your site configuration(eg: mysite) 
Add the following to the location part of the server block
```
server {
    server_name yourdomain.com www.yourdomain.com;

    location / {
        proxy_pass http://localhost:8001; #whatever port your app runs on
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```
```
#make a symbolic copy of the config file
sudo ln -s /etc/nginx/sites-available/your-file-name /etc/nginx/sites-enabled

# Check NGINX config
sudo nginx -t

# Restart NGINX
sudo nginx -s reload
```

## 8. Add SSL with LetsEncrypt
```
sudo add-apt-repository ppa:certbot/certbot
sudo apt-get update
sudo apt-get install python3-certbot-nginx
sudo certbot --nginx -d yourdomain.com -d www.yourdomain.com

# Only valid for 90 days, test the renewal process with
certbot renew --dry-run
```
