# Deprecation notice
This repo has been deprecated in favour of [polkachu repo](https://github.com/polkachu/cosmos-validators).

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
touch ~/.ssh/authorized_keys
# Put your ssh public key into `~/.ssh/authorized_keys`
chmod 400 ~/.ssh/authorized_keys
```

Set the following parameterts in the `/etc/ssh/sshd_config` to disable password login:
```bash
ChallengeResponseAuthentication no
PasswordAuthentication no
UsePAM no
PermitRootLogin no
```
Then reload the sshd:
```bash
sudo systemctl reload ssh
```

### Run playbooks

Install dependencies:
```bash
ansible-galaxy install -r requirements.yml
```

#### Node exporter (mac)
Prerequisites:
 - install `gnu-tar` dependency
([doc](https://galaxy.ansible.com/ui/repo/published/prometheus/prometheus/content/role/node_exporter/))
 - disable fork safety in order to prevent
`may have been in progress in another thread when fork() was called.` error
([stackoverflow](https://stackoverflow.com/questions/50168647/multiprocessing-causes-python-to-crash-and-gives-an-error-may-have-been-in-progr))
 - DNS should be setup: `domain` should point to the managed server
 - Generate `htpasswd` file for basic auth ([guide](https://docs.nginx.com/nginx/admin-guide/security-controls/configuring-http-basic-authentication/)) and put it at `playbooks/templates/nginx/htpasswd`

After that:
```bash
ansible-playbook -i inventory.yml --ask-become-pass -l -v monitored playbooks/ensure-nginx.yml
ansible-playbook -i inventory.yml --ask-become-pass -l -v monitored playbooks/letsencrypt.yml
brew install gnu-tar
OBJC_DISABLE_INITIALIZE_FORK_SAFETY=YES \
ansible-playbook -i inventory.yml --ask-become-pass -l -v monitored playbooks/node-exp.yml
```

Once successful, metrics show be accessible at `https://<domain>/prometheus-node/metrics` with basic auth.
There are several guides about prometheus ang grafane setup to monitor and alert on node metrics,
[this](https://medium.com/@DanialEskandari/system-monitoring-with-prometheus-grafana-and-node-exporter-412027684564)
is one of them.

#### Band
To run preset:
```bash
ansible-playbook -i inventory.yml -v --ask-become-pass -l band playbooks/preset.yml
```

To install, sync and run band node:
```bash
ansible-playbook -i inventory.yml -v --ask-become-pass -l band playbooks/band/node.yml
```

#### Galactica
To run preset:
```bash
ansible-playbook -i inventory.yml -v --ask-become-pass -l galactica playbooks/preset.yml
```

To install, sync and run galactica node:
```bash
ansible-playbook -i inventory.yml -v --ask-become-pass -l galactica playbooks/galactica.yml
```

#### Zero Gravity
To run preset:
```bash
ansible-playbook -i inventory.yml -v --ask-become-pass -l zg playbooks/preset.yml
```
To install, sync and run zero gravity node:
```bash
ansible-playbook -i inventory.yml -v --ask-become-pass -l zg playbooks/zg.yml
```

After that it's up to you to expose public API endpoints on the node, create a
new validator or put existing validator's keys on the node.

#### Expose public endpoints
To run preset:
```bash
ansible-playbook -i inventory.yml --ask-become-pass -l apiNodes -v playbooks/preset.yml
```
Ensure Nginx installed:
```bash
ansible-playbook -i inventory.yml --ask-become-pass -l apiNodes -v playbooks/ensure-nginx.yml
```
Issue certificates (all domains in question should point to the managed server):
```bash
ansible-playbook -i inventory.yml --ask-become-pass -l apiNodes -v playbooks/api-node-letsencrypt.yml
```
Setup Nginx config:
```bash
ansible-playbook -i inventory.yml --ask-become-pass -l apiNodes -v playbooks/api-node-nginx.yml
```

### TODO:
- [ ] Avoid any action if there is synced node on the same port already
- [ ] Unbreak certs renewal (standalone domain validation may conflict with Nginx listening 80 port)
- [ ] Implement playbook to enable API endpoints on the node
- [ ] Galactica: use ansible.builtin.git in order to pull code
- [ ] Galactica: use ansible.builtin.template for systemd service config

Feel free to add more playbooks and send PRs, you are welcome!

### Credits
Great thanks to [STAVR](https://stavr-team.gitbook.io/nodes-guides) and
[NODERS](https://noders.services/) teams for their services websites.
