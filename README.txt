# STOP!
# Use official image!
# REF: https://hub.docker.com/r/grafana/grafana-arm32v7-linux/tags

# To get a tag list, add this bash function
function list-docker-tags(){
    wget -q https://registry.hub.docker.com/v1/repositories/$1/tags -O -  | sed -e 's/[][]//g' -e 's/"//g' -e 's/ //g' | tr '}' '\n'  | awk -F: '{print $3}' | grep -v ^master
}

list-docker-tags grafana/grafana-enterprise

docker run -d --name=grafana -p 3000:3000 grafana/grafana-enterprise:8.4.4
docker ps
# http://RaspberryPiAddress:3000 <-- admin/admin and set a new password
docker exec -it grafana bash
ls -alF /etc/grafana
ls -alF /var/lib/grafana
#docker stop grafana
#docker rm grafana


####################################################
# Grafana (in a Docker Container) for Raspberry Pi #
#                         REF: https://grafana.com #
####################################################
# ARM 64bit for Raspberry Pi:
REF: https://grafana.com/grafana/download/8.4.4?platform=docker

###############################################################################
# First time setup #
####################
# Grafana (stand-alone for Docker Swarm)
# REF: https://hub.docker.com/r/grafana/grafana/
# and http://docs.grafana.org/installation/configuration/

# Create the bind mount directories and copy files to the correct node based on constraints in the docker-compose!
sudo mkdir -p /opt/docker/grafana/etc/grafana
sudo mkdir -p /opt/docker/grafana/var/lib/grafana
#sudo chown -R 472:472 /opt/docker/grafana
sudo chmod a+rw -R /opt/docker/grafana

##########
# Deploy #
##########
# Deploy the stack into a Docker Swarm
docker stack deploy -c docker-compose.yml grafana
#docker stack rm grafana

# Verify
docker service ls | grep grafana
docker service logs -f grafana_grafana
###############################################################################
