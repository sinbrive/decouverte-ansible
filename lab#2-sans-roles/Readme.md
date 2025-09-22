# ansible sans roles

```
ansible-nginx-lb/
├── ansible.cfg
├── inventory.ini
├── webservers.yaml
├── loadbalancer.yaml
├── templates/
│   ├── nginx_round_robin.conf.j2
│   ├── nginx_least_conn.conf.j2
│   └── nginx_ip_hash.conf.j2
├── files/
│   └── index.html


```

## Déploiement
```
ansible-playbook webservers.yaml
ansible-playbook loadbalancer.yaml -e lb_mode=round_robin
ansible-playbook loadbalancer.yaml -e lb_mode=least_conn
ansible-playbook loadbalancer.yaml -e lb_mode=ip_hash
```

