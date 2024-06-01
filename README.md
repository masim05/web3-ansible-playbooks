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
