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

## Commands Line
```bash
Install ansible

 sudo apt update
 sudo apt install ansible

Create an inventory file (add the IP address for each server on its own line)

172.16.250.132
172.16.250.133
172.16.250.134

Add the inventory file to version control

 git add inventory

Commit the changes

 git commit -m "first version of the inventory file, added three hosts."

Push commit to Github

git push origin master

Test Ansible is working

ansible all --key-file ~/.ssh/ansible -i inventory -m ping

Create ansible config file

nano ansible.cfg
 
[defaults]
inventory = inventory private_key_file = ~/.ssh/ansible

Now the ansible command can be simplified

ansible all -m ping

List all of the hosts in the inventory

ansible all --list-hosts

Gather facts about your hosts

ansible all -m gather_facts

Gather facts about your hosts, but limit it to just one host

ansible all -m gather_facts --limit 172.16.250.132

Tell ansible to use sudo (become)

ansible all -m apt -a update_cache=true --become --ask-become-pass

Install a package via the apt module

ansible all -m apt -a name=vim-nox --become --ask-become-pass

Install a package via the apt module, and also make sure it’s the latest version available

ansible all -m apt -a "name=snapd state=latest" --become --ask-become-pass

Upgrade all the package updates that are available

ansible all -m apt -a upgrade=dist --become --ask-become-passxx

install_apache.yml

 ---
 
 - hosts: all
   become: true
   tasks:
 
   - name: install apache2 package
     apt:
       name: apache2

Run the playbook

 ansible-playbook --ask-become-pass install_apache.yml

install_apache.yml (second version)

 ---
 
 - hosts: all
   become: true
   tasks:
 
   - name: update repository index
     apt:
       update_cache: yes
 
   - name: install apache2 package
     apt:
       name: apache2

install_apache.yml (third version)

 ---
 
 - hosts: all
   become: true
   tasks:
 
   - name: update repository index
     apt:
       update_cache: yes
 
   - name: install apache2 package
     apt:
     name: apache2
 
   - name: add php support for apache
     apt:
       name: libapache2-mod-php

install_apache.yml (fourth version)

 ---
 
 - hosts: all
   become: true
   tasks:
 
   - name: update repository index
     apt:
       update_cache: yes
 
   - name: install apache2 package
     apt:
       name: apache2
       state: latest
 
   - name: add php support for apache
     apt:
       name: libapache2-mod-php
       state: latest

remove_apache.yml

 ---
 
 - hosts: all
   become: true
   tasks:
 
   - name: remove apache2 package
     apt:
       name: apache2
       state: absent
 
   - name: remove php support for apache
     apt:
       name: libapache2-mod-php
       state: absent

install_apache.yml

 ---
 
 - hosts: all
   become: true
   tasks:
 
   - name: update repository index
     apt:
       update_cache: yes
     when: ansible_distribution == "Ubuntu"
 
   - name: install apache2 package
     apt:
       name: apache2
       state: latest
     when: ansible_distribution == "Ubuntu"
 
   - name: add php support for apache
     apt:
       name: libapache2-mod-php
       state: latest
     when: ansible_distribution == "Ubuntu"

Gather facts, while limiting to a single host

 ansible all -m gather_facts --limit 172.16.250.248

install_apache.yml (updated to include centos)

 ---
 
 - hosts: all
   become: true
   tasks:
 
   - name: update repository index
     apt:
       update_cache: yes
     when: ansible_distribution == "Ubuntu"
 
   - name: install apache2 package
     apt:
       name: apache2
       state: latest
     when: ansible_distribution == "Ubuntu"
 
   - name: add php support for apache
     apt:
       name: libapache2-mod-php
       state: latest
     when: ansible_distribution == "Ubuntu"
 
   - name: update repository index
     dnf:
       update_cache: yes
     when: ansible_distribution == "CentOS"
 
   - name: install httpd package
     dnf:
       name: httpd
       state: latest
     when: ansible_distribution == "CentOS"
 
   - name: add php support for apache
     dnf:
       name: php
       state: latest
     when: ansible_distribution == "CentOS"

install_apache.yml (previous version)

 ---
 
 - hosts: all
   become: true
   tasks:
 
   - name: update repository index for Ubuntu
     apt:
       update_cache: yes
     when: ansible_distribution == "Ubuntu"
 
   - name: install apache2 package for Ubuntu
     apt:
       name: apache2
       state: latest
     when: ansible_distribution == "Ubuntu"
 
   - name: add php support for apache for Ubuntu
     apt:
       name: libapache2-mod-php for CentOS
       state: latest
     when: ansible_distribution == "Ubuntu"
 
   - name: update repository index for CentOS
     dnf:
       update_cache: yes
     when: ansible_distribution == "CentOS"
 
   - name: install httpd package for CentOS
     dnf:
       name: httpd
       state: latest
     when: ansible_distribution == "CentOS"
 
   - name: add php support for apache for CentOS
     dnf:
       name: php
       state: latest
     when: ansible_distribution == "CentOS"

install_apache.yml (condensed)

 ---
 
 - hosts: all
   become: true
   tasks:
 
   - name: update repository index
     apt:
       update_cache: yes
     when: ansible_distribution == "Ubuntu"
 
   - name: install apache2 and php packages for Ubuntu
     apt:
       name:
         - apache2
         - libapache2-mod-php
       state: latest
     when: ansible_distribution == "Ubuntu"
 
   - name: update repository index
     dnf:
       update_cache: yes
     when: ansible_distribution == "CentOS"
 
   - name: install apache and php packages for CentOS
     dnf:
       name:
         - httpd
         - php
       state: latest
     when: ansible_distribution == "CentOS"
   

install_apache.yml (further condensed)

 ---
 
 - hosts: all
   become: true
   tasks:
 
   - name: install apache2 package
     apt:
       name:
         - apache2
         - libapache2-mod-php
       state: latest
       update_cache: yes
     when: ansible_distribution == "Ubuntu"
 
   - name: install httpd package
     dnf:
       name:
         - httpd
         - php
       state: latest
       update_cache: yes
     when: ansible_distribution == "CentOS"

install_apache.yml (condensed even futher)

 ---
 
 - hosts: all
   become: true
   tasks:
 
   - name: install apache and php
     package:
       name:
         - "Template:Apache package"
         - "Template:Php package"
       state: latest
       update_cache: yes

inventory file (with host variables added)

172.16.250.132 apache_package=apache2 php_package=libapache2-mod-php
172.16.250.133 apache_package=apache2 php_package=libapache2-mod-php
172.16.250.134 apache_package=apache2 php_package=libapache2-mod-php
172.16.250.248 apache_package=httpd php_package=php

---

install_apache.yml

 ---
 
 - hosts: all
   become: true
   tasks:
 
   - name: install apache and php for Ubuntu servers
     apt:
       name:
         - apache2
         - libapache2-mod-php
       state: latest
       update_cache: yes
     when: ansible_distribution == "Ubuntu"
 
   - name: install apache and php for CentOS servers
     dnf:
       name:
         - httpd
         - php
       state: latest
       update_cache: yes
     when: ansible_distribution == "CentOS"

inventory (updated with groups)

[web_servers]
172.16.250.132
172.16.250.248
 
[db_servers]
172.16.250.133

[file_servers]
172.16.250.134

site.yml

 ---
 
 - hosts: all
   become: true
   tasks:
 
   - name: install updates (CentOS)
     dnf:
       update_only: yes
       update_cache: yes
     when: ansible_distribution == "CentOS"
 
   - name: install updates (Ubuntu)
     apt:
       upgrade: dist
       update_cache: yes
     when: ansible_distribution == "Ubuntu"
 
 
 - hosts: web_servers
   become: true
   tasks:
 
   - name: install apache and php for Ubuntu servers
     apt:
       name:
         - apache2
         - libapache2-mod-php
       state: latest
     when: ansible_distribution == "Ubuntu"
 
   - name: install apache and php for CentOS servers
     dnf:
       name:
         - httpd
         - php
       state: latest
     when: ansible_distribution == "CentOS"

site.yml (second version)

 ---
 
 - hosts: all
   become: true
   pre_tasks:
 
   - name: install updates (CentOS)
     dnf:
       update_only: yes
       update_cache: yes
     when: ansible_distribution == "CentOS"
 
   - name: install updates (Ubuntu)
     apt:
       upgrade: dist
       update_cache: yes
     when: ansible_distribution == "Ubuntu"
 
 
 - hosts: web_servers
   become: true
   tasks:
 
   - name: install apache2 package
     apt:
       name:
         - apache2
         - libapache2-mod-php
       state: latest
     when: ansible_distribution == "Ubuntu"
 
   - name: install httpd package
     dnf:
       name:
         - httpd
         - php
       state: latest
     when: ansible_distribution == "CentOS"

site.yml (third version)

 ---
 
 - hosts: all
   become: true
   pre_tasks:
 
   - name: install updates (CentOS)
     dnf:
       update_only: yes
       update_cache: yes
     when: ansible_distribution == "CentOS"
 
   - name: install updates (Ubuntu)
     apt:
       upgrade: dist
       update_cache: yes
    when: ansible_distribution == "Ubuntu"
 
 
 - hosts: web_servers
   become: true
   tasks:
 
   - name: install httpd package (CentOS)
     dnf:
       name:
         - httpd
         - php
       state: latest
     when: ansible_distribution == "CentOS"
 
   - name: install apache2 package (Ubuntu)
     apt:
       name:
         - apache2
         - libapache2-mod-php
       state: latest
     when: ansible_distribution == "Ubuntu"
 
 - hosts: db_servers
   become: true
   tasks:
 
   - name: install httpd package (CentOS)
     dnf:
       name: mariadb
       state: latest
     when: ansible_distribution == "CentOS"
 
   - name: install mariadb server
     apt:
       name: mariadb-server
       state: latest
     when: ansible_distribution == "Ubuntu"

site.yml (fourth version)

 ---9
 
 - hosts: all
   become: true
   pre_tasks:
 
   - name: install updates (CentOS)
     dnf:
       update_only: yes
       update_cache: yes
     when: ansible_distribution == "CentOS"
 
   - name: install updates (Ubuntu)
     apt:
       upgrade: dist
       update_cache: yes
     when: ansible_distribution == "Ubuntu"
 
 
 - hosts: web_servers
   become: true
   tasks:
 
   - name: install httpd package (CentOS)
     dnf:
       name:
         - httpd
         - php
       state: latest
     when: ansible_distribution == "CentOS"
 
   - name: install apache2 package (Ubuntu)
     apt:
       name:
         - apache2
         - libapache2-mod-php
       state: latest
     when: ansible_distribution == "Ubuntu"
 
 - hosts: db_servers
   become: true
   tasks:
 
   - name: install mariadb server package (CentOS)
     dnf:
       name: mariadb
       state: latest
     when: ansible_distribution == "CentOS"
 
   - name: install mariadb server
     apt:
       name: mariadb-server
       state: latest
     when: ansible_distribution == "Ubuntu"
 
 - hosts: file_servers
   become: true
   tasks:
 
   - name: install samba package
     package:
       name: samba
       state: latest

---10

site.yml (with tags added)

 ---
 
 - hosts: all
   become: true
   pre_tasks:
 
   - name: install updates (CentOS)
     tags: always
     dnf:
       update_only: yes
       update_cache: yes
     when: ansible_distribution == "CentOS"
 
   - name: install updates (Ubuntu)
     tags: always
     apt:
       upgrade: dist
       update_cache: yes
     when: ansible_distribution == "Ubuntu"
 
 
 - hosts: web_servers
   become: true
   tasks:
 
   - name: install httpd package (CentOS)
     tags: apache,centos,httpd
     dnf:
       name:
         - httpd
         - php
       state: latest
     when: ansible_distribution == "CentOS"
 
   - name: install apache2 package (Ubuntu)
     tags: apache,apache2,ubuntu
     apt:
       name:
         - apache2
         - libapache2-mod-php
       state: latest
     when: ansible_distribution == "Ubuntu"
 
 - hosts: db_servers
   become: true
   tasks:
 
   - name: install mariadb server package (CentOS)
     tags: centos,db,mariadb
     dnf:
       name: mariadb
       state: latest
     when: ansible_distribution == "CentOS"
 
   - name: install mariadb server
     tags: db,mariadb,ubuntu
     apt:
       name: mariadb-server
       state: latest
     when: ansible_distribution == "Ubuntu"
 
 - hosts: file_servers
   tags: samba
   become: true
   tasks:
 
   - name: install samba package
     tags: samba
     package:
       name: samba
       state: latest

List the available tags in a playbook

ansible-playbook --list-tags site_with_tags.yml

Examples of running a playbook but targeting specific tags

 ansible-playbook --tags db --ask-become-pass site_with_tags.yml
 ansible-playbook --tags centos --ask-become-pass site_with_tags.yml
 ansible-playbook --tags apache --ask-become-pass site_with_tags.yml

---11
default_site.html

Note: Store this file in a directory named “files” in the root of the repository.

 <html>
     <title>Web-site test</title>
     <body>
        Ansible is awesome!
    </body>
 </html>

site.yml (updated to copy default_site.html)

 ---
 
 - hosts: all
   become: true
   pre_tasks:
 
   - name: install updates (CentOS)
     tags: always
     dnf:
       update_only: yes
       update_cache: yes
     when: ansible_distribution == "CentOS"
 
   - name: install updates (Ubuntu)
     tags: always
     apt:
       upgrade: dist
       update_cache: yes
     when: ansible_distribution == "Ubuntu"
 
 
 - hosts: web_servers
   become: true
   tasks:
 
   - name: install httpd package (CentOS)
     tags: apache,centos,httpd
     dnf:
       name:
         - httpd
         - php
       state: latest
     when: ansible_distribution == "CentOS"
 
   - name: install apache2 package (Ubuntu)
     tags: apache,apache2,ubuntu
     apt:
       name:
         - apache2
         - libapache2-mod-php
       state: latest
     when: ansible_distribution == "Ubuntu"
 
   - name: copy html file for site
     tags: apache,apache,apache2,httpd
     copy:
       src: default_site.html
       dest: /var/www/html/index.html
       owner: root
       group: root
       mode: 0644
 
 - hosts: db_servers
   become: true
   tasks:
 
   - name: install mariadb server package (CentOS)
     tags: centos,db,mariadb
     dnf:
       name: mariadb
       state: latest
     when: ansible_distribution == "CentOS"
 
   - name: install mariadb server
     tags: db,mariadb,ubuntu
     apt:
       name: mariadb-server
       state: latest
     when: ansible_distribution == "Ubuntu"
 
 - hosts: file_servers
   tags: samba
   become: true
   tasks:
 
   - name: install samba package
     tags: samba
     package:
       name: samba
       state: latest

Run the playbook

 ansible-playbook --ask-become-pass file_management.yml

file_management.yml (updated)

 ---
 
 - hosts: all
   become: true
   pre_tasks:
 
   - name: install updates (CentOS)
     tags: always
     dnf:
       update_only: yes
       update_cache: yes
     when: ansible_distribution == "CentOS"
 
   - name: install updates (Ubuntu)
     tags: always
     apt:
       upgrade: dist
       update_cache: yes
     when: ansible_distribution == "Ubuntu"
 
 - hosts: workstations
   become: true
   tasks:

   - name: install unzip
     package:
       name: unzip
 
   - name: install terraform
     unarchive:
      src: https://releases.hashicorp.com/terraform/0.12.28/terraform_0.12.28_linux_amd64.zip
       dest: /usr/local/bin
       remote_src: yes
       mode: 0755
       owner: root
       group: root
 
 - hosts: web_servers
   become: true
   tasks:
 
   - name: install httpd package (CentOS)
     tags: apache,centos,httpd
     dnf:
       name:
         - httpd
         - php
       state: latest
     when: ansible_distribution == "CentOS"
 
   - name: install apache2 package (Ubuntu)
     tags: apache,apache2,ubuntu
     apt:
       name:
         - apache2
         - libapache2-mod-php
       state: latest
     when: ansible_distribution == "Ubuntu"
 
   - name: copy html file for site
     tags: apache,apache,apache2,httpd
     copy:
       src: default_site.html
       dest: /var/www/html/index.html
       owner: root
       group: root
       mode: 0644
 
 - hosts: db_servers
   become: true
   tasks:
 
   - name: install mariadb server package (CentOS)
     tags: centos,db,mariadb
     dnf:
       name: mariadb
       state: latest
     when: ansible_distribution == "CentOS"
 
   - name: install mariadb server
     tags: db,mariadb,ubuntu
     apt:
       name: mariadb-server
       state: latest
     when: ansible_distribution == "Ubuntu"
 
 - hosts: file_servers
   tags: samba
   become: true
   tasks:
 
   - name: install samba package
     tags: samba
     package:
       name: samba
       state: latest

Run the updated playbook

 ansible-playbook --ask-become-pass file_management.yml

---12
site.yml (text in bold has been added since the previous version)

 ---
 
 - hosts: all
   become: true
   pre_tasks:
 
   - name: install updates (CentOS)
     tags: always
     dnf:
       update_only: yes
       update_cache: yes
     when: ansible_distribution == "CentOS"
 
   - name: install updates (Ubuntu)
     tags: always
     apt:
       upgrade: dist
       update_cache: yes
     when: ansible_distribution == "Ubuntu"
 
 - hosts: workstations
   become: true
   tasks:
 
   - name: install unzip
     package:
       name: unzip
 
   - name: install terraform
     unarchive:
       src: https://releases.hashicorp.com/terraform/0.12.28/terraform_0.12.28_linux_amd64.zip
       dest: /usr/local/bin
       remote_src: yes
       mode: 0755
       owner: root
       group: root
 
 - hosts: web_servers
   become: true
   tasks:
 
   - name: install httpd package (CentOS)
     tags: apache,centos,httpd
     dnf:
       name:
         - httpd
         - php
       state: latest
     when: ansible_distribution == "CentOS"
 
   - name: start and enable httpd (CentOS)
     tags: apache,centos,httpd
     service:
       name: httpd
       state: started
     when: ansible_distribution == "CentOS"
 
   - name: install apache2 package (Ubuntu)
     tags: apache,apache2,ubuntu
     apt:
       name:
         - apache2
         - libapache2-mod-php
       state: latest
     when: ansible_distribution == "Ubuntu"
 
   - name: copy html file for site
     tags: apache,apache,apache2,httpd
     copy:
       src: default_site.html
       dest: /var/www/html/index.html
       owner: root
       group: root
       mode: 0644
 
 - hosts: db_servers
   become: true
   tasks:
 
   - name: install mariadb server package (CentOS)
     tags: centos,db,mariadb
     dnf:
       name: mariadb
       state: latest
     when: ansible_distribution == "CentOS"
 
   - name: install mariadb server
     tags: db,mariadb,ubuntu
     apt:
       name: mariadb-server
       state: latest
     when: ansible_distribution == "Ubuntu"
 
 - hosts: file_servers
   tags: samba
   become: true
   tasks:
 
   - name: install samba package
     tags: samba
     package:
       name: samba
       state: latest

site.yml (second version, the change is in bold)

 ---
 
 - hosts: all
   become: true
   pre_tasks:
 
   - name: install updates (CentOS)
     tags: always
     dnf:
       update_only: yes
       update_cache: yes
     when: ansible_distribution == "CentOS"
 
   - name: install updates (Ubuntu)
     tags: always
     apt:
       upgrade: dist
       update_cache: yes
     when: ansible_distribution == "Ubuntu"
 
 - hosts: workstations
   become: true
   tasks:
 
   - name: install unzip
     package:
       name: unzip
 
   - name: install terraform
     unarchive:
       src: https://releases.hashicorp.com/terraform/0.12.28/terraform_0.12.28_linux_amd64.zip
       dest: /usr/local/bin
       remote_src: yes
       mode: 0755
       owner: root
       group: root
 
 - hosts: web_servers
   become: true
   tasks:
 
   - name: install httpd package (CentOS)
     tags: apache,centos,httpd
     dnf:
       name:
         - httpd
         - php
       state: latest
     when: ansible_distribution == "CentOS"
 
   - name: start and enable httpd (CentOS)
     tags: apache,centos,httpd
     service:
       name: httpd
       state: started
       enabled: yes
     when: ansible_distribution == "CentOS"
 
   - name: install apache2 package (Ubuntu)
     tags: apache,apache2,ubuntu
     apt:
       name:
         - apache2
         - libapache2-mod-php
       state: latest
     when: ansible_distribution == "Ubuntu"
 
   - name: copy html file for site
     tags: apache,apache,apache2,httpd
     copy:
       src: default_site.html
       dest: /var/www/html/index.html
       owner: root
       group: root
       mode: 0644
 
 - hosts: db_servers
   become: true
   tasks:
 
   - name: install mariadb server package (CentOS)
     tags: centos,db,mariadb
     dnf:
       name: mariadb
       state: latest
     when: ansible_distribution == "CentOS"
 
   - name: install mariadb server
     tags: db,mariadb,ubuntu
     apt:
       name: mariadb-server
       state: latest
     when: ansible_distribution == "Ubuntu"
 
 - hosts: file_servers
   tags: samba
   become: true
   tasks:
 
   - name: install samba package
     tags: samba
     package:
       name: samba
       state: latest

site.yml (added ‘lineinfile’ play)

 ---
 
 - hosts: all
   become: true
   pre_tasks:
 
   - name: install updates (CentOS)
     tags: always
     dnf:
       update_only: yes
       update_cache: yes
     when: ansible_distribution == "CentOS"
 
   - name: install updates (Ubuntu)
     tags: always
     apt:
       upgrade: dist
       update_cache: yes
     when: ansible_distribution == "Ubuntu"
 
 - hosts: workstations
   become: true
   tasks:
 
   - name: install unzip
     package:
       name: unzip
 
   - name: install terraform
     unarchive:
       src: https://releases.hashicorp.com/terraform/0.12.28/terraform_0.12.28_linux_amd64.zip
       dest: /usr/local/bin
       remote_src: yes
       mode: 0755
       owner: root
       group: root
 
 - hosts: web_servers
   become: true
   tasks:
 
   - name: install httpd package (CentOS)
     tags: apache,centos,httpd
     dnf:
       name:
         - httpd
         - php
       state: latest
     when: ansible_distribution == "CentOS"
 
   - name: start and enable httpd (CentOS)
     tags: apache,centos,httpd
     service:
       name: httpd
       state: started
       enabled: yes
     when: ansible_distribution == "CentOS"
 
   - name: install apache2 package (Ubuntu)
     tags: apache,apache2,ubuntu
     apt:
       name:
         - apache2
         - libapache2-mod-php
       state: latest
     when: ansible_distribution == "Ubuntu"
 
   - name: change e-mail address for admin
     tags: apache,centos,httpd
     lineinfile:
       path: /etc/httpd/conf/httpd.conf
       regexp: '^ServerAdmin'
       line: ServerAdmin somebody@somewhere.net
     when: ansible_distribution == "CentOS"
     register: httpd
 
   - name: restart httpd (CentOS)
     tags: apache,centos,httpd
     service:
       name: httpd
       state: restarted
     when: httpd.changed 
 
   - name: copy html file for site
     tags: apache,apache,apache2,httpd
     copy:
       src: default_site.html
       dest: /var/www/html/index.html
       owner: root
       group: root
       mode: 0644
 
 - hosts: db_servers
   become: true
   tasks:
 
   - name: install mariadb server package (CentOS)
     tags: centos,db,mariadb
     dnf:
       name: mariadb
       state: latest
     when: ansible_distribution == "CentOS"
 
   - name: install mariadb server
     tags: db,mariadb,ubuntu
     apt:
       name: mariadb-server
       state: latest
     when: ansible_distribution == "Ubuntu"
 
 - hosts: file_servers
   tags: samba
   become: true
   tasks:
 
   - name: install samba package
     tags: samba
     package:
       name: samba
       state: latest

```

## Playbook

```yml
---
- name: Install Apache
  hosts: webserver     # targeting a group of hosts named webserver
  become: true
  tasks:
    - name: Install Apache
      apt:
        name: apache2
        state: latest
    - name: Start Apache
      service:
        name: apache2
        state: started

```
```
ansible-playbook install-apache.yml
```


## sources
- 
