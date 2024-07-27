# first create Virtual machine using AWS or local virtual machine 



# update package repository 

sudo apt update 
sudo apt upgrade 

# Add Adoptium repository

wget -O - https://packages.adoptium.net/artifactory/api/gpg/key/public | tee /etc/apt/keyrings/adoptium.asc
echo "deb [signed-by=/etc/apt/keyrings/adoptium.asc] https://packages.adoptium.net/artifactory/deb $(awk -F= '/^VERSION_CODENAME/{print$2}' /etc/os-release) main" | tee /etc/apt/sources.list.d/adoptium.list



# Install Java 17

apt update
apt install temurin-17-jdk
/usr/bin/java --version


# Install Jenkins 

 curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee \
  /usr/share/keyrings/jenkins-keyring.asc > /dev/null
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins


# Starting Jenkins
sudo systemctl start jenkins


# check jenkins 

sudo systemctl status jenkins


# i installed java and jenkins using vagrant on my VM 


# sc install nginx as reverse proxy to give jenkins specific name 

sudo apt install nginx
sudo apt status nginx
# http://your_server_ip

sudo vi /etc/nginx/sites-available/jenkins.Magdy.cloud

upstream jenkins{
    server 127.0.0.1:8080;
}

server{
    listen      80;
    server_name jenkins.cloud.Magdy;

    access_log  /var/log/nginx/jenkins.access.log;
    error_log   /var/log/nginx/jenkins.error.log;

    proxy_buffers 16 64k;
    proxy_buffer_size 128k;

    location / {
        proxy_pass  http://jenkins;
        proxy_next_upstream error timeout invalid_header http_500 http_502 http_503 http_504;
        proxy_redirect off;

        proxy_set_header    Host            $host;
        proxy_set_header    X-Real-IP       $remote_addr;
        proxy_set_header    X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header    X-Forwarded-Proto https;
    }

}

# Next, let’s enable the file by creating a link from it to the sites-enabled directory, which Nginx reads from during startup

# Run from shell prompt (replace your domain)
sudo ln -s /etc/nginx/sites-available/jenkins.Magdy.cloud /etc/nginx/sites-enabled/


# Next, test to make sure that there are no syntax errors in any of your Nginx files:

sudo nginx -t
sudo systemctl restart nginx

=============================================
# Configure Jenkins with SSL Using an Nginx Reverse Proxy

# 1- Installing Certbot

sudo apt install certbot python3-certbot-nginx


# 2- Confirming Nginx’s Configuration


sudo vi /etc/nginx/sites-available/jenkins.cloud.Magdy

# 3- Obtaining an SSL Certificate
sudo certbot --nginx -d jenkins.Magdy.cloud


# Verifying Certbot Auto-Renewal

sudo systemctl status certbot.timer


# creating another VM agent jenkins 

sudo apt update
sudo apt upgrade

# Create Jenkins User
sudo adduser jenkins
# Grant Sudo Rights to Jenkins User

sudo usermod -aG sudo jenkins


# Adoptium Java 17 
sudo bash


# Add Adoptium repository
wget -O - https://packages.adoptium.net/artifactory/api/gpg/key/public | sudo tee /etc/apt/trusted.gpg.d/adoptium.asc


# Add the Adoptium APT Repository:

echo "deb [signed-by=/etc/apt/trusted.gpg.d/adoptium.asc] https://packages.adoptium.net/artifactory/deb $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/adoptium.list


# Install Java 17 
apt update
sudo apt-get install -y temurin-17-jdk

# Docker


# Install using the repository

# Update the apt package index and install packages to allow apt to use a repository over HTTPS:
sudo apt-get update

sudo apt-get install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release




# Add Docker’s official GPG key:


sudo mkdir -m 0755 -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg



# Use the following command to set up the repository

echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null



# Install Docker Engine


sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin



# Manage Docker as a non-root user

# Create the docker group + Add your user to the docker group  + Verify that you can run docker commands without sudo.
sudo groupadd docker
sudo usermod -aG docker $USER
newgrp docker
docker run hello-world
ssh jenkins@$AGENT_HOSTNAME


