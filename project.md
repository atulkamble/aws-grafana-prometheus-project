```
3100, 8082, 8081, 9090, 3000

sudo yum install git -y
git --version
git clone https://github.com/grafana/tutorial-environment.git
cd tutorial-environment/
docker --version
sudo yum install docker -y
docker --version
sudo systemctl start docker
sudo systemctl enable docker
sudo docker ps
sudo docker images
sudo usermod -aG docker $USER
newgrp docker

sudo systemctl stop grafana-server
sudo systemctl stop prometheus

sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose

docker-compose up -d
```
