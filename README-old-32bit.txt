# STOP!
# Use official image!
# REF: https://hub.docker.com/r/grafana/grafana-arm32v7-linux/tags
#docker pull grafana/grafana-arm32v7-linux:latest
docker run -d --name=grafana -p 3000:3000 grafana/grafana-arm32v7-linux:6.0.2
# http://YourRaspberryPiIpAddress:3000
# REF: https://github.com/grafana/grafana/tree/master/packaging/docker


# REF https://stackoverflow.com/questions/24481564/how-can-i-find-a-docker-image-with-a-specific-tag-in-docker-registry-on-the-dock
# To get a tag list, add this bash function
function list-dh-tags(){
    wget -q https://registry.hub.docker.com/v1/repositories/$1/tags -O -  | sed -e 's/[][]//g' -e 's/"//g' -e 's/ //g' | tr '}' '\n'  | awk -F: '{print $3}' | grep -v ^master
}

list-dh-tags grafana/grafana-arm32v7-linux

# Note: Older 32bit ARM no lnger used.
# Use 64bit on Raspberry Pi
# https://grafana.com/grafana/download/8.4.4?platform=docker
#docker run -d --name=grafana -p 3000:3000 grafana/grafana-enterprise:8.4.4
list-dh-tags grafana/grafana-enterprise



####################################################
# Grafana (in a Docker Container) for Raspberry Pi #
#                        REF: https://grafana.com/ #
####################################################
# ARM build/download for Raspberry Pi:
REF: https://grafana.com/grafana/download/6.0.1?platform=arm

###############################################################################
# Docker build
sudo bash
time docker build --no-cache -t ernestgwilsonii/docker-raspberry-pi-grafana:6.0.1 -f Dockerfile.armhf .
docker images

# Verify 
docker run -it -p 3000:3000 ernestgwilsonii/docker-raspberry-pi-grafana:6.0.1
# From another ssh session:
#docker ps

# Upload to Docker Hub
docker login
docker push ernestgwilsonii/docker-raspberry-pi-grafana:6.0.1
###############################################################################


###############################################################################
# First time setup #
####################
# Grafana (stand-alone for Docker Swarm)
# REF: https://hub.docker.com/r/grafana/grafana/
# and http://docs.grafana.org/installation/configuration/

# Create the bind mount directories and copy files to the correct node based on constraints in the docker-compose!
mkdir -p /opt/docker/grafana
cp grafana.crt /opt/docker/grafana/
cp grafana.key /opt/docker/grafana/
cp ldap.toml /opt/docker/grafana/
chown -R 472:472 /opt/docker/grafana
chmod a+rw -R /opt/docker/grafana

##########
# Deploy #
##########
# Deploy the stack into a Docker Swarm
docker stack deploy -c docker-compose.yml grafana
# docker stack rm grafana

# Verify
docker service ls | grep grafana
docker service logs -f grafana
###############################################################################
