# Provision Raspberry Pi
Uses Ansible to set up Docker, Docker-Compose and provision the server

## Steps to perform before running the Ansible Playbook
- `apt update`
- `apt upgrade`
- change hostname
- `sudo apt install ansible`
- git get the provisioning repo
- `cd rpi-provision`
- `ansible-playbook provision.yaml`

## Running the provisioning script
At present I am assuming that this playbook is run locally, and not on remote hosts.

## My hardware
I am using a Raspberry Pi 4b with 4 GB RAM and 128 GB micro SD card

## Acknowledgments
- https://github.com/glennklockwood/rpi-ansible
