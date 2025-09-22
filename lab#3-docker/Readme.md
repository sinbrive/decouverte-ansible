# ansible avec docker compose


```
ansible-docker-lb/
├── docker-compose.yaml
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

## setup containers

```
docker-compose up -d
docker exec web1 apt update && docker exec web1 apt install -y openssh-server sudo python3
docker exec web2 apt update && docker exec web2 apt install -y openssh-server sudo python3
docker exec loadbalancer apt update && docker exec loadbalancer apt install -y openssh-server sudo python3


```

## mot de passe de conteneurs + ssh
```
for c in web1 web2 loadbalancer; do
  docker exec $c bash -c "echo 'root:root' | chpasswd"
  docker exec $c service ssh start
done

```

## déploiement
```
ansible-playbook webservers.yaml
ansible-playbook loadbalancer.yaml -e lb_mode=round_robin

```
- http://localhost:8080 pour tester le load balancing.