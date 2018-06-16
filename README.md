# ansible-101

## Table of contents
- [Config Management](#config-management)
- [Challenges](#challenges)
- [Ansible](#ansible)
- [Basic Structure](#basic-structure)
  * [Playbooks](#playbooks)
  * [Handlers](#handlers)
  * [Iventory](#inventory)
  * [Group Variabls](#group-vars)
- [Running a playbook](#running-a-playbook)


## Config Management
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
  - SSH for unix/linux
  - Winrm for Windows
 
## Basic Structure

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

### Handlers
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

### Inventory
 - /etc/ansible/hosts
 - or pass inventory flag -i <path>
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
 
### Group Vars
 - store variables which are shared across multiple tasks playbooks and servers
 ```
# can optionally end in '.yml', '.yaml', or '.json'
/etc/ansible/group_vars/all.yml
/etc/ansible/group_vars/us-east-1/dbservers.yml
/etc/ansible/group_vars/us-east-1/webservers.yml
/etc/ansible/group_vars/ap-southeast-2/dbservers.yml
/etc/ansible/group_vars/ap-southeast-2/webservers.yml

 ```
 
 ### Directory layout
 ```
 production                # inventory file for production servers
staging                   # inventory file for staging environment

group_vars/
   group1                 # here we assign variables to particular groups
   group2                 # ""
host_vars/
   hostname1              # if systems need specific variables, put them here
   hostname2              # ""

library/                  # if any custom modules, put them here (optional)
module_utils/             # if any custom module_utils to support modules, put them here (optional)
filter_plugins/           # if any custom filter plugins, put them here (optional)

site.yml                  # master playbook
webservers.yml            # playbook for webserver tier
dbservers.yml             # playbook for dbserver tier

roles/
    common/               # this hierarchy represents a "role"
        tasks/            #
            main.yml      #  <-- tasks file can include smaller files if warranted
        handlers/         #
            main.yml      #  <-- handlers file
        templates/        #  <-- files for use with the template resource
            ntp.conf.j2   #  <------- templates end in .j2
        files/            #
            bar.txt       #  <-- files for use with the copy resource
            foo.sh        #  <-- script files for use with the script resource
        vars/             #
            main.yml      #  <-- variables associated with this role
        defaults/         #
            main.yml      #  <-- default lower priority variables for this role
        meta/             #
            main.yml      #  <-- role dependencies
        library/          # roles can also include custom modules
        module_utils/     # roles can also include custom module_utils
        lookup_plugins/   # or other types of plugins, like lookup in this case

    webtier/              # same kind of structure as "common" was above, done for the webtier role
    monitoring/           # ""
    fooapp/        
 ```
 - roles (role and module development is slightly advance topic)
 ```
 ---
- hosts: webservers
  roles:
     - common
     - webservers
 ```
 - templates
   * jinja2 templates
   * {{ my_variable }} - can be used in plabooks and template files
```
- template:
    src: foo.j2
    dest: /etc/file.conf
    owner: bin
    group: wheel
    mode: 0644
```
 - files
   You can place files in this directory, your role will automatically know where to looks for the file
```
- name: example copying file with owner and permissions
  copy:
    src: foo.conf
    dest: /etc/foo.conf
    owner: foo
    group: foo
    mode: 0644

```

## Running a playbook
```
ansible-playbook playbook.yml
ansible-playbook playbook.yml -vvvv
ansible-playbook playbook.yml --syntax-check
ansible-playbook playbook.yml --list-hosts
ansible-playbook foo.yml --check
```

## Misc
- include_tasks
- import_tasks
- import_playbook
- tags
```
tasks:
- import_tasks: common_tasks.yml
# or
- include_tasks: common_tasks.yml
```


