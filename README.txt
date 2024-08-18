# first create Virtual machine using AWS or local virtual machine 
# 5 Vms 1- jenkins-ui , 2- jenkins-agent , argocd, k8s-agent , soanrqube 




# first we will install jenkins-ui 
# update package repository 

sudo apt update 
sudo apt upgrade 


# Install Java 17

sudo apt install openjdk-17-jdk-headless
apt update
java --version


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



# OPTIONAL sc install nginx as reverse proxy to give jenkins specific name 

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
# Optional Configure Jenkins with SSL Using an Nginx Reverse Proxy

# 1- Installing Certbot

sudo apt install certbot python3-certbot-nginx


# 2- Confirming Nginx’s Configuration


sudo vi /etc/nginx/sites-available/jenkins.cloud.Magdy

# 3- Obtaining an SSL Certificate
sudo certbot --nginx -d jenkins.Magdy.cloud


# Verifying Certbot Auto-Renewal

sudo systemctl status certbot.timer


# second VM jenkins-agent

#Adding an SSH Based Agent to Jenkins

sudo apt update
sudo apt upgrade

# Create Jenkins User
sudo adduser jenkins

# Grant Sudo Rights to Jenkins User

sudo usermod -aG sudo jenkins


# Adoptium Java 17 
sudo bash

#install java 17 
sudo apt install openjdk-17-jdk-headless


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
===============================
===============================
# from jenkins-ui 
ssh jenkins@HOST_NAME


# Create private and public SSH keys. 
# The following command creates the private key jenkinsAgent_rsa and the public key jenkinsAgent_rsa.pub. 
# It is recommended to store your keys 
# under ~/.ssh/ so we move to that directory before creating the key pair.

 mkdir ~/.ssh; cd ~/.ssh/ && ssh-keygen -t rsa -m PEM -C "Jenkins agent key" -f "jenkinsAgent_rsa"

# Add the public SSH key to the list of authorized keys on the agent machine
cat jenkinsAgent_rsa.pub >> ~/.ssh/authorized_keys

# Ensure that the permissions of the ~/.ssh directory is secure, 
# as most ssh daemons will refuse to use keys 
# that have file permissions that are considered insecure:

chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys ~/.ssh/jenkinsAgent_rsa

# Copy the private SSH key (~/.ssh/jenkinsAgent_rsa) from the agent machine to your OS clipboard


cat ~/.ssh/jenkinsAgent_rsa
#Now you can add the Agent on the Jenkins UI (Controller)
=====================================================================
=====================================================================
# 3d vm sonarqube 


sudo apt update
sudo apt upgrade


Add PostgresSQL repository¶

sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'
wget -qO- https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo tee /etc/apt/trusted.gpg.d/pgdg.asc &>/dev/null



Install PostgreSQL¶

sudo apt update
sudo apt-get -y install postgresql postgresql-contrib
sudo systemctl enable postgresql


Create Database for Sonarqube¶


sudo passwd postgres
su - postgres
createuser sonar



createuser sonar
psql 
ALTER USER sonar WITH ENCRYPTED password 'sonar';
CREATE DATABASE sonarqube OWNER sonar;
grant all privileges on DATABASE sonarqube to sonar;
\q
exit






# Install Java 17

sudo apt install openjdk-17-jdk-headless
apt update
java --version

sudo vim /etc/security/limits.conf

sonarqube   -   nofile   65536
sonarqube   -   nproc    4096




sudo vim /etc/sysctl.conf


vm.max_map_count = 262144




sudo reboot




sudo wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-9.9.0.65466.zip
sudo apt install unzip
sudo unzip sonarqube-9.9.0.65466.zip -d /opt
sudo mv /opt/sonarqube-9.9.0.65466 /opt/sonarqube


sudo groupadd sonar
sudo useradd -c "user to run SonarQube" -d /opt/sonarqube -g sonar sonar
sudo chown sonar:sonar /opt/sonarqube -R


sudo vim /opt/sonarqube/conf/sonar.properties




sonar.jdbc.username=sonar
sonar.jdbc.password=sonar
sonar.jdbc.url=jdbc:postgresql://localhost:5432/sonarqube




sudo vim /etc/systemd/system/sonar.service





[Unit]
Description=SonarQube service
After=syslog.target network.target

[Service]
Type=forking

ExecStart=/opt/sonarqube/bin/linux-x86-64/sonar.sh start
ExecStop=/opt/sonarqube/bin/linux-x86-64/sonar.sh stop

User=sonar
Group=sonar
Restart=always

LimitNOFILE=65536
LimitNPROC=4096

[Install]
WantedBy=multi-user.target











sudo systemctl start sonar
sudo systemctl enable sonar
sudo systemctl status sonar

sudo tail -f /opt/sonarqube/logs/sonar.log



http://<IP>:9000







install argocd and k8s 


sudo apt update
sudo apt upgrade



sudo bash
curl -sfL https://get.k3s.io | INSTALL_K3S_EXEC="server" sh -s - --disable traefik
exit 
mkdir .kube
sudo cp /etc/rancher/k3s/k3s.yaml ./config
sudo chown dmistry:dmistry config
chmod 400 config
export KUBECONFIG=~/.kube/config




kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml






kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "NodePort"}}' 








kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d


































