```

mkdir ansible-nginx-lb && cd ansible-nginx-lb

sudo apt update
sudo apt install ansible

ssh-copy-id ubuntu@192.168.1.10  # Load balancer
ssh-copy-id ubuntu@192.168.1.11  # Web1
ssh-copy-id ubuntu@192.168.1.12  # Web2

#test connxion

ansible all -m ping
```

## supervision des serveurs

```yml

- name: Supervision des serveurs web et du load balancer
  hosts: all
  become: yes
  tasks:

    - name: Vérifier la connectivité
      ping:

    - name: Afficher le nom d’hôte
      command: hostname
      register: hostname_result

    - name: Afficher le nom d’hôte (résultat)
      debug:
        msg: "Nom d’hôte : {{ hostname_result.stdout }}"

    - name: Vérifier l’état du service Apache (webservers uniquement)
      service:
        name: apache2
        state: started
      when: "'webservers' in group_names"

    - name: Vérifier l’état du service Nginx (load balancer uniquement)
      service:
        name: nginx
        state: started
      when: "'loadbalancer' in group_names"

    - name: Afficher la charge système
      shell: uptime
      register: uptime_result

    - name: Afficher la charge système (résultat)
      debug:
        msg: "{{ inventory_hostname }} → {{ uptime_result.stdout }}"

```

```
ansible-playbook supervision.yaml

```