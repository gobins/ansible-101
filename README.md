# ansible-101
Table of contents
=================

<!--ts-->
   * [Config Managemet](##Config-Management)
   * [Challenges](##Challenges)
   * [Ansible](##Ansible)
   * [Communication](##Communication)
   * [Usage](#usage)
      * [STDIN](#stdin)
      * [Local files](#local-files)
      * [Remote files](#remote-files)
      * [Multiple files](#multiple-files)
      * [Combo](#combo)
      * [Auto insert and update TOC](#auto-insert-and-update-toc)
   * [Tests](#tests)
   * [Dependency](#dependency)
<!--te-->

Config Management
=================
  - OS Configuration
  - Users, Files, Services, Permissions
  - Chef(ruby), Puppet(ruby), Ansible(python), CFEngine(c), Salstack(python)

## Challenges
  - Handcrafted machines
  - Config Drift
  - Not reproducible and buggy
  - Time consuming

## Ansible
  - Agentless
  - Config is written in yaml
  - Very easy to learn
  - Easy to read

## Communication
  - SSH for unix/linux
  - Winrm for Windows
 
## Basic Structure of Ansible code

### Playbooks
```
---
- hosts: webservers
  vars:
    http_port: 80
    max_clients: 200
  remote_user: ec2-user
  become: yes
  become_user: my-user
  tasks:
  - name: ensure apache is at the latest version
    yum:
      name: httpd
      state: latest
  - name: write the apache config file
    template:
      src: /srv/httpd.j2
      dest: /etc/httpd.conf
    notify:
    - restart apache
  - name: ensure apache is running (and enable it at boot)
    service:
      name: httpd
      state: started
      enabled: yes
  handlers:
    - name: restart apache
      service:
        name: httpd
        state: restarted
```

#### Handlers
```
handlers:
    - name: restart memcached
      service:
        name: memcached
        state: restarted
      listen: "restart web services"
    - name: restart apache
      service:
        name: apache
        state:restarted
      listen: "restart web services"

tasks:
    - name: restart everything
      command: echo "this task will restart the web services"
      notify: "restart web services"
```

#### Running a playbook
```
ansible-playbook playbook.yml
ansible-playbook playbook.yml -vvvv
ansible-playbook playbook.yml --syntax-check
ansible-playbook playbook.yml --list-hosts
ansible-playbook foo.yml --check
```
### Inventory
```
  ---
  all:
    hosts:
      mail.example.com:
    children:
      webservers:
        hosts:
          foo.example.com:
          bar.example.com: 
```
