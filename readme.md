# Homelab Provisioning
Use Docker Swarm (and less ideally, Docker Compose) to manage deployments. 

## Steps to perform when installing a new server
- On a clean Ubuntu server
- `apt update`
- `apt upgrade`
- Setup SSH keys (if going to commit)
- `git clone https://github.com/dalehumby/homelab.git`
- `cd homelab`

## Directory layout
Containers assume that all files are available on a mount `/media/cluster`. Each service has its own sub-directory in the cluster directory. (TODO describe network mount / glusterfs)

## Docker Swarm
I am running 2x [Raspberry Pi 4B](https:///www.raspberrypi.org/products/raspberry-pi-4-model-b/) with 4 GB RAM and 32 GB Micro SD card in [Docker Swarm mode](https://docs.docker.com/engine/swarm/), and a Dell x86 server with 24 GB RAM. 

There are several Stacks that define various services:
- [`home-stack.yaml`](home-stack.yaml): IoT and home automation services
- [`proxy-stack.yaml`](proxy-stack.yaml): Run Traefik proxy server, including SSL termination, so can access services from the internet
- [`cron-stack.yaml`](cron-stack.yaml): Launch periodic jobs like updating dynamic DNS and removing old containers

### Deploy a stack
For example, the IoT "home" services: `docker stack deploy -c home-stack.yaml home`

This creates a `home_default` network and each service is prefixed with `home_` eg `home_homeassistant`.

`docker service ls` to see which services are running.

Use [secrets](https://docs.docker.com/engine/swarm/secrets/) and [configs](https://docs.docker.com/engine/swarm/configs/) so I don't have to commit sensitive info to repo.

## Docker Compose
Swarm cannot pass through devices (like Zigbee USB or USB SDR dongles), and anyway the container needs to run on the host where the device is.

- `compose.yaml`: Containers that run on the primary host (Dell)

To deploy the services: `docker compose up -d`

Using .env for secrets, not commmited to repo.
