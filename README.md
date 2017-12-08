# Custom DigitalOcean Playbooks

These are some Ansible playbooks that I use to create a digitalocean droplet and then configure it the way I like. It provisions a (very small, currently) Debian 9/x64 system and does some configuration to make it the way I like.

## Getting started
Install the required python libraries. I prefer to use a virtualenv when doing python in order to keep the libraries separate; if this is not desired, omit lines 3 and 4 from the following block
```
git clone git@github.com:kuzmik/ansible-droplet.git
cd ansible-droplet
mkvirtualenv droplet
workon droplet
pip install -r requirements.txt
ansible-playbook create.yaml
```

A whole bunch of stuff will scroll by, followed up by:
```
PLAY RECAP ***********************
localhost                  : ok=6    changed=2    unreachable=0    failed=0
test                       : ok=53   changed=36   unreachable=0    failed=0
```

Whole process takes like 3.5 minutes.

## Tasks
* `create.yaml`: runs all of the tasks linked below to create and configure the droplet
* `destory.yaml`: destroys a droplet by name

Sub tasks:

* `tasks/digitalocean.yaml`: creates the droplet
* `tasks/install-packages.yaml`: installs some base packages that I use
* `tasks/configure-system.yaml`: random system config stuff, like disabling ipv6
* `tasks/remove-system.yaml`: installs sysvinit and *completely eradicates* systemd from the system (other than the shim library to let modern apps function sans systemd); includes two separate reboots
* `tasks/create-user.yaml`: creates my user, associates my key with the new user and drops a bashrc in the homedir
* `tasks/security.yaml`: *super* basic system hardening, including adding my user to sudoers
* `tasks/install-lego.yaml`: download the lego release, untar it, copy the binary to /usr/bin, and cleanup /tmp
* `tasks/sshd.yaml`: further secures sshd; changes the port on which sshd listens to the configured port and disables root login; must be run last in the sequence because of this

I unashamedly cribbed most of this stuff from other projects as I was learning ansible.

## TODO
* Setup a light-nginx install, with properly configured ssl params
* Playbook to add a virtual host to nginx by name, complete with letsencrypt ssl cert
