# # Exemple de load balancer avec ible


## 🧱 Structure du projet

```plaintext
project/
├── docker-compose.yml
├── loadbalancer/
│   └── nginx.conf
├── server1/
│   └── index.html
├── server2/
│   └── index.html
├── ansible/
│   ├── inventory
│   └── playbook.yml
```

---

## 📦 `docker-compose.yml`

```yaml
version: '3.8'

services:
  loadbalancer:
    image: ubuntu
    container_name: loadbalancer
    command: sleep infinity
    ports:
      - "8080:80"
    networks:
      - lbnet

  server1:
    image: ubuntu
    container_name: server1
    command: sleep infinity
    networks:
      - lbnet

  server2:
    image: ubuntu
    container_name: server2
    command: sleep infinity
    networks:
      - lbnet

networks:
  lbnet:
    driver: bridge
```

---

## 🗂️ `ansible/inventory`

```ini
[loadbalancer]
loadbalancer ansible_connection=docker

[webservers]
server1 ansible_connection=docker
server2 ansible_connection=docker
```

---

## 📜 `ansible/playbook.yml`

```yaml
- name: Configuration du Load Balancer
  hosts: loadbalancer
  become: true
  tasks:
    - name: Mettre à jour apt
      apt:
        update_cache: yes

    - name: Installer Nginx
      apt:
        name: nginx
        state: present

    - name: Copier la configuration Nginx
      copy:
        src: ../loadbalancer/nginx.conf
        dest: /etc/nginx/nginx.conf

    - name: Redémarrer Nginx
      service:
        name: nginx
        state: restarted

- name: Configuration des serveurs web
  hosts: webservers
  become: true
  tasks:
    - name: Mettre à jour apt
      apt:
        update_cache: yes

    - name: Installer Nginx
      apt:
        name: nginx
        state: present

    - name: Copier index.html pour server1
      copy:
        src: ../server1/index.html
        dest: /usr/share/nginx/html/index.html
      when: inventory_hostname == "server1"

    - name: Copier index.html pour server2
      copy:
        src: ../server2/index.html
        dest: /usr/share/nginx/html/index.html
      when: inventory_hostname == "server2"

    - name: Redémarrer Nginx
      service:
        name: nginx
        state: restarted
```

---

## 📝 `loadbalancer/nginx.conf`

```nginx
events {}

http {
  upstream backend {
    server server1;
    server server2;
  }

  server {
    listen 80;

    location / {
      proxy_pass http://backend;
    }
  }
}
```

---

## 📝 `server1/index.html`

```html
<h1>Bienvenue sur le Serveur 1</h1>
```

## 📝 `server2/index.html`

```html
<h1>Bienvenue sur le Serveur 2</h1>
```

---

## 🚀 Étapes de déploiement

1. **Lancer les conteneurs** :

```bash
docker-compose up -d
```

2. **Exécuter le playbook Ansible** :

```bash
cd ansible
ansible-playbook -i inventory playbook.yml
```

3. **Tester le load balancing** :

```bash
curl http://localhost:8080
curl http://localhost:8080
curl http://localhost:8080
```

---
