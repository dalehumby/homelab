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

Then start the services:
`docker-compose up -d`

## Directory layout
Using `services.yaml`, Ansible will create 2 directories in the `/media/cluster` directory:
- `config`: configuration used when starting the service, assumed to not change while the service is running
- `data`: Read/Write space for the service while it is running.

Each service has its own sub-directory in the `config` and `data` directories.

I am using [GlusterFS](https://www.gluster.org/) to mirror the `cluster` volume that contains the `config` and `data` directories between all of my Pi's so that containers have access to config/data no matter which Pi Docker Swarm schedules them to. Setting up Gluster is not yet in the Ansible playbooks.

## My hardware
I am using a [Raspberry Pi 4B](https://www.raspberrypi.org/products/raspberry-pi-4-model-b/) with 4 GB RAM and 128 GB Micro SD card

## Acknowledgments
- https://github.com/glennklockwood/rpi-ansible
