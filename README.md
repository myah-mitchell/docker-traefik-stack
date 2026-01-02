# Docker Traefik Stack


## Create needed folders

```bash
mkdir -p /opt/docker/volumes/traefik/traefik-logs
mkdir -p /opt/docker/volumes/traefik/cert-data
mkdir -p /opt/docker/volumes/traefik/traefik-plugins
chmod -R 770 /opt/docker/volumes/traefik/*
sudo chown -R $USER:101000 /opt/docker/volumes/traefik
sudo chown -R 101000:101000 /opt/docker/volumes/traefik/*
```

