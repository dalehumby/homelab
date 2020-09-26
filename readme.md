# Provision Raspberry Pi
Uses Ansible to set up Docker, Docker-Compose and provision the server

## Steps to perform before running the Ansible Playbook
- Change the default password `passwd`
- `apt update`
- `apt upgrade`
- change hostname
- `sudo apt install ansible`
- `git clone https://github.com/dalehumby/rpi-provision.git`
- `cd rpi-provision`

## Run the provisioning script
At present I am assuming that this playbook is run locally, and not on remote hosts.
- Set up base OS: `ansible-playbook provision.yaml`
- Set up files and folders for the services: `ansible-playbook services.yaml`

Then start the services:
`docker-compose up -d`

(TODO auto run this after restart)

## My hardware
I am using a [Raspberry Pi 4B](https://www.raspberrypi.org/products/raspberry-pi-4-model-b/) with 4 GB RAM and 128 GB Micro SD card

## Acknowledgments
- https://github.com/glennklockwood/rpi-ansible
