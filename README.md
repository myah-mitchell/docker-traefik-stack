# Docker Traefik Stack

Note this is still early in design and lots of this will change before being merged into main.

## Create needed folders

```bash
mkdir -p /opt/docker/volumes/traefik/traefik-logs
mkdir -p /opt/docker/volumes/traefik/cert-data
mkdir -p /opt/docker/volumes/traefik/traefik-plugins
chmod -R 770 /opt/docker/volumes/traefik/*
sudo chown -R $USER:101000 /opt/docker/volumes/traefik
sudo chown -R 101000:101000 /opt/docker/volumes/traefik/*
```

## How to run the service

For each server you will start one of the compose files, only one should be ran per server as the have overlaping services.

**Redis Server Stack** The `compose-server.yaml` will start up a Traefik stack with Traefik-kop and a password protected Redis server that all Traefik-kop services can write too. This server will need to allow inbound 6379 (Redis) and then port 80/443 (HTTP/HTTPS). This compose file should only be deployed to one server. Other containers can be deployed on this server.

**Redis Client Stack** The `compose-client.yaml` will start up a Traefik stack that is configured to Redis replicate data from the compose-server stack and then using a Traefik Provider add routers and services from Redis. This stack is intended to be used on the edge of your network in a heavily restricted DMZ network. Only Port 443 (HTTPS) to your internal Traefik servers, and port 6379 (Redis) to the server running `compose-server.yaml` need to be opened out of this restricted DMZ. This compose file can be deployed one or more servers in DMZ. The idea is only things like CrowdSec are also deployed here as this is your HTTP/HTTPS Bastion host.

**Traefik-kop Stack** The `compose-kop.yaml` will start up Traefik stack with Traefik-kop. This server will only need port 80/443 (HTTP/HTTPS) inbound open. This server will need port 6379 (Redis) open outbound to talk to the `compose-server.yaml` stack. This compose file can be ran on as many servers as you would like. Each server just needs to be able to acces the Redis server (6379 outbound) and be able to be accessed by the DMZ servers (443 inbound). Other containers can be deployed on this server.