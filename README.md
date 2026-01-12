# Docker Traefik Stack

Note this is still early in design and lots of this will change before being merged into main.

## Create needed folders

```bash
mkdir -p /opt/docker/volumes/traefik/traefik-logs
mkdir -p /opt/docker/volumes/traefik/cert-data
mkdir -p /opt/docker/volumes/traefik/traefik-plugins
mkdir -p /opt/docker/volumes/traefik/vlagent-data
mkdir -p /opt/docker/volumes/traefik/vmagent-data
mkdir -p /opt/docker/volumes/traefik/vector-data
sudo chmod 750 /opt/docker/volumes/traefik/
sudo chown $USER:101000 /opt/docker/volumes/traefik
sudo chown 101000:101000 /opt/docker/volumes/traefik/*
```

## How to run the service

For each server you will start one of the compose files, only one should be ran per server as the have overlaping services.

**Control Server Stack (Server)** The `compose-server.yaml` will start up a Traefik stack with Traefik-kop and a password protected Redis server that all Traefik-kop services can write too. This server will need to allow inbound 6379 (Redis) and then port 80/443 (HTTP/HTTPS). This compose file should only be deployed to one server. 

**Bastion Host Stack (DMZ)** The `compose-dmz.yaml` will start up a Traefik stack that is configured to have Redis replicate data from the server stack. Then using a Traefik provider add routers and services from Redis to the proxy config. This stack is intended to be used on the edge of your network in a heavily restricted DMZ network. Only Port 443 (HTTPS) to your internal Traefik servers, and port 6379 (Redis) to the server running `compose-server.yaml` need to be opened for trafic out of this restricted DMZ. This compose file can be deployed to one or more servers in the DMZ. This is your HTTP/HTTPS Bastion host.

**Traefik-kop Stack (Agent)** The `compose-agent.yaml` will start up a Traefik stack with Traefik-kop. This server will only need port 80/443 (HTTP/HTTPS) inbound open. This server will need port 6379 (Redis) outbound open to talk to the `compose-server.yaml` stack. This compose file can be ran on as many servers as you would like. Each server just needs to be able to acces the Redis server (6379 outbound) and be accessed by the DMZ servers (443 inbound). 

**Traefik Stack (Basic)** The `compose-basic.yaml` will start up a basic Traefik stack without Traefik-kop support. This is a working Traefik stack with a socket-proxy and custom error pages.

**Test Stack** The `compose.yaml` wil start up a test Traefik stack with Traefik-kop and Redis. In this stack Traefik has also has a Redis provider so it will allow single host testing of Traefik and Traefik-kop labels. 