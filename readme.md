# Provision Raspberry Pi
Uses Ansible to set up Docker, Docker-Compose and provision the server

## Steps to perform before running the Ansible Playbook
- Change the default password `passwd`
- `apt update`
- `apt upgrade`
- change hostname using `sudo raspi-config` under Networking
- setup static IP `sudo nano /etc/dhcpcd.conf`
- `sudo apt install ansible`
- `sudo apt install git`
- Setup SSH keys (if going to commit)
- `git clone https://github.com/dalehumby/rpi-provision.git`
- `cd rpi-provision`

## Run the provisioning scripts
At present I am assuming that this playbook is run locally, and not on remote hosts.
- Set up base OS: `ansible-playbook provision.yaml`
- Set up files and directories for the services: `ansible-playbook services.yaml`
- `sudo reboot`

## Directory layout
Using `services.yaml`, Ansible will create 2 directories in the `/media/cluster` directory:
- `config`: configuration used when starting the service, assumed to not change while the service is running
- `data`: Read/Write space for the service while it is running.

Each service has its own sub-directory in the `config` and `data` directories.

I am using [GlusterFS](https://www.gluster.org/) to mirror the `cluster` volume that contains the `config` and `data` directories between all of my Pi's so that containers have access to config/data no matter which Pi Docker Swarm schedules them to. Setting up Gluster is not yet in the Ansible playbooks.

## Docker Swarm
I am running 2x [Raspberry Pi 4B](https://www.raspberrypi.org/products/raspberry-pi-4-model-b/) with 4 GB RAM and 32 GB Micro SD card in [Docker Swarm mode](https://docs.docker.com/engine/swarm/), with 1 Master and 1 Worker. This doesn't provide high availability (only 1 Master), but does add extra capacity. Mostly I wanted to play with Docker Swarm. 

I have several Stacks (Docker Compose files) that define various services
- `home-stack.yaml`: IoT and home automation services
- `proxy-stack.yaml`: Run Traefik proxy server, including SSL termination, so can access services from the internet
- `cron-stack.yaml`: Launch periodic jobs like updating DNS (dynamic DNS)

### Deploy a stack
For example, the IoT "home" services: `docker stack deploy -c home-stack.yaml home`

This creates a `home_default` network and each service is prefixed with `home_` eg `home_grafana`.

`docker service ls` to see which services are running.

Use [secrets](https://docs.docker.com/engine/swarm/secrets/) and [configs](https://docs.docker.com/engine/swarm/configs/) so I don't have to commit sensitive info to repo.
