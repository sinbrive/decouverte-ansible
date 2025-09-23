# decouverte-ansible

<img width="800" height="400" alt="Ansible-architecture" src="https://github.com/user-attachments/assets/2bd75544-2394-4ff5-9d9f-39f4851679d9" />


---

# Exemple de projet Ansible 

# Pré-requis

Avant de lancer le projet, assure-toi que le serveur maître (machine où Ansible est installé) dispose des éléments suivants :

### 1. 🔧 Installer Ansible (Ubuntu/Debian)

```bash
sudo apt update
sudo apt install -y software-properties-common
sudo add-apt-repository --yes --update ppa:ansible/ansible
sudo apt install -y ansible
```

Vérifie l’installation :

```bash
ansible --version
```

---

### 2. Créer une clé SSH

```bash
ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa -N ""
```

Cela crée :
- `~/.ssh/id_rsa` → clé privée
- `~/.ssh/id_rsa.pub` → clé publique

---

### 3. Copier la clé publique sur les serveurs distants

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub ubuntu@192.168.1.10  # Load balancer
ssh-copy-id -i ~/.ssh/id_rsa.pub ubuntu@192.168.1.11  # Web1
ssh-copy-id -i ~/.ssh/id_rsa.pub ubuntu@192.168.1.12  # Web2
```

Tu devras entrer le mot de passe `ubuntu` une seule fois pour chaque machine.

---

### 4. Vérifier la connexion SSH sans mot de passe

```bash
ssh ubuntu@192.168.1.10
```

Tu dois être connecté directement sans mot de passe. Répète pour les autres IPs.

---

# Structure du projet

```
mon-projet-ansible/
├── ansible.cfg
├── inventory.ini
├── playbook.yaml
├── html/
│   └── index.html
├── nginx_configs/
│   ├── nginx_round_robin.conf
│   ├── nginx_least_conn.conf
│   └── nginx_ip_hash.conf
```

---

# Configuration

### `ansible.cfg`

```ini
[defaults]
inventory = inventory.ini
host_key_checking = False
remote_user = ubuntu
private_key_file = ~/.ssh/id_rsa
```

---

### `inventory.ini`

```ini
[webservers]
web1 ansible_host=192.168.1.11
web2 ansible_host=192.168.1.12

[loadbalancer]
lb ansible_host=192.168.1.10
```

---

# `playbook.yaml`

```yaml
- name: Déploiement Apache sur les webservers
  hosts: webservers
  become: yes
  tasks:
    - name: Installer Apache
      apt:
        name: apache2
        state: present
        update_cache: yes

    - name: Copier la page d’accueil
      copy:
        src: html/index.html
        dest: /var/www/html/index.html

    - name: Démarrer Apache
      service:
        name: apache2
        state: started
        enabled: yes

- name: Déploiement Nginx sur le load balancer
  hosts: loadbalancer
  become: yes
  vars:
    lb_mode: round_robin
  tasks:
    - name: Installer Nginx
      apt:
        name: nginx
        state: present
        update_cache: yes

    - name: Choisir le fichier de configuration Nginx
      set_fact:
        nginx_config_file: "nginx_configs/nginx_{{ lb_mode }}.conf"

    - name: Copier le fichier de configuration Nginx
      copy:
        src: "{{ nginx_config_file }}"
        dest: /etc/nginx/nginx.conf

    - name: Redémarrer Nginx
      service:
        name: nginx
        state: restarted
        enabled: yes
```

---

# Commandes de déploiement

```bash
ansible-playbook playbook.yaml
ansible-playbook playbook.yaml -e lb_mode=least_conn
ansible-playbook playbook.yaml -e lb_mode=ip_hash
```
