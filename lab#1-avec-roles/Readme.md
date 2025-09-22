# ansible avec roles

## Objectif

## Déployer automatiquement via Ansible :

- 1 VM en tant que load balancer avec Nginx
- 2 VM en tant que serveurs web avec Apache ou Nginx
- Tester différents algorithmes de load balancing : round-robin, least connections, IP hash

##  Prérequis

- 3 VM Ubuntu accessibles en SSH
- Ansible installé sur votre machine de contrôle (sudo apt install ansible)
- Clés SSH configurées pour un accès sans mot de passe
- Python 3 installé sur les VM

## Structure du projet

```
ansible-nginx-lb/
├── ansible.cfg
├── inventory.ini
├── playbooks/
│   ├── load_balancer.yaml
│   └── web_servers.yaml
├── roles/
│   ├── nginx_lb/
│   │   ├── tasks/
│   │   │   └── main.yaml
│   │   └── templates/
│   │       └── nginx.conf.j2
│   └── webserver/
│       ├── tasks/
│       │   └── main.yaml
│       └── files/
│           └── index.html


```

## Run
```
ansible-playbook playbooks/web_servers.yaml
ansible-playbook playbooks/load_balancer.yaml
```

## Validation

- Accédez à l’IP du load balancer dans votre navigateur : http://192.168.1.10 
- Rechargez plusieurs fois pour voir la répartition.