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
mkdir -p ~/.ssh
# Put your ssh public key into `~/.ssh/authorized_keys`
chmod 400 ~/.ssh/authorized_keys
```

### Run

Install dependencies:
```bash
ansible-galaxy install -r requirements.yml
```

#### Node exporter (mac)
Need to install `gnu-tar` dependency
([doc](https://galaxy.ansible.com/ui/repo/published/prometheus/prometheus/content/role/node_exporter/))
and disable fork safety in order to prevent
`may have been in progress in another thread when fork() was called.` error
([stackoverflow](https://stackoverflow.com/questions/50168647/multiprocessing-causes-python-to-crash-and-gives-an-error-may-have-been-in-progr)).
```bash
ansible-playbook -i inventory.yml --ask-become-pass -l monitored playbooks/ensure-nginx.yml
ansible-playbook -i inventory.yml --ask-become-pass -l monitored playbooks/letsencrypt.yml
brew install gnu-tar
OBJC_DISABLE_INITIALIZE_FORK_SAFETY=YES \
ansible-playbook -i inventory.yml --ask-become-pass -l monitored playbooks/node-exp.yml
```


#### Nodes
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

### TODO:
- [ ] Avoid any action if there is synced node on the same port already

Feel free to add more playbooks and send PRs, you are welcome!

### Credits
Great thanks to [STAVR](https://stavr-team.gitbook.io/nodes-guides) and
[NODERS](https://noders.services/) teams for their services websites.