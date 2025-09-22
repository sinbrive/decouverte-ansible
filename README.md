# decouverte-ansible




<img width="800" height="400" alt="Ansible-architecture" src="https://github.com/user-attachments/assets/2bd75544-2394-4ff5-9d9f-39f4851679d9" />
## Install
```sh
sudo apt-get update
sudo apt-get install ansible
```

> You can specify the inventory file by adding the following line to your ansible.cfg file:
```
inventory = /path/to/your/inventory/file

export ANSIBLE_INVENTORY=/path/to/your/inventory/file # in case container or ci/cd pipeline
```
