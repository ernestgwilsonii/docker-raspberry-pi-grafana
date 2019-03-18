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
mkdir -p /opt/grafana
cp grafana.crt /opt/grafana/
cp grafana.key /opt/grafana/
cp ldap.toml /opt/grafana/
chown -R 472:472 /opt/grafana
chmod a+rw -R /opt/grafana

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