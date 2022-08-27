# Homelab Provisioning
Use Docker Swarm to manage deployments. 

## Steps to perform before running the Ansible Playbook
- On a clean Ubuntu server
- `apt update`
- `apt upgrade`
- Setup SSH keys (if going to commit)
- `git clone https://github.com/dalehumby/homelab.git`
- `cd homelab`

## Directory layout
Containers assume that all files are available on a mount `/media/cluster`. Each service has its own sub-directory in the cluster directory.

## Docker Swarm
I am running 2x [Raspberry Pi 4B](https://www.raspberrypi.org/products/raspberry-pi-4-model-b/) with 4 GB RAM and 32 GB Micro SD card in [Docker Swarm mode](https://docs.docker.com/engine/swarm/), and a Dell x86 server with 32 GB RAM. 

There are several Stacks (Docker Compose files) that define various services:
- `home-stack.yaml`: IoT and home automation services
- `proxy-stack.yaml`: Run Traefik proxy server, including SSL termination, so can access services from the internet
- `cron-stack.yaml`: Launch periodic jobs like updating DNS (dynamic DNS)

### Deploy a stack
For example, the IoT "home" services: `docker stack deploy -c home-stack.yaml home`

This creates a `home_default` network and each service is prefixed with `home_` eg `home_grafana`.

`docker service ls` to see which services are running.

Use [secrets](https://docs.docker.com/engine/swarm/secrets/) and [configs](https://docs.docker.com/engine/swarm/configs/) so I don't have to commit sensitive info to repo.
