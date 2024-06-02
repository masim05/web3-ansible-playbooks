### Why ansible?
Ansible automates the management of remote systems and controls their desired
state. This repo contains several playbooks to preset, install, sync and
run validating nodes.

### Install ansible
Follow [official doc](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html)
to install ansible.

### Setup managed hosts
On every managed host create `ansible` user with `sudo` permissions:
```bash
sudo adduser ansible
sudo usermod -aG sudo ansible
sudo su - ansible
# Put your ssh public key into `~/.ssh/authorized_keys`
chmod 400 ~/.ssh/authorized_keys
```

### Run
To run preset:
```bash
ansible-playbook -i inventory.yml -v --ask-become-pass playbooks/preset.yml
```

To install, sync and run galactica node:
```bash
ansible-playbook -i inventory.yml -v --ask-become-pass -l galactica playbooks/galactica.yml
```
To install, sync and run zero gravity node:
```bash
ansible-playbook -i inventory.yml -v --ask-become-pass -l zg playbooks/zg.yml
```

After that it's up to you to expose public API endpoints on the node, create a
new validator or put existing validator's keys on the node.

Feel free to add more playbooks and send PRs, you are welcome!

### Credits
Great thanks to [STAVR](https://stavr-team.gitbook.io/nodes-guides) and
[NODERS](https://noders.services/) teams for their services websites.