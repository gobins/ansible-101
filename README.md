# ansible-101

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

## Communication
  - SSH for unix/linux
  - Winrm for Windows
 
## Basic Structure of Ansible code

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
