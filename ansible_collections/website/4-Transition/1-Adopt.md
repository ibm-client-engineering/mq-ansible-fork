##### *Ansible* installation ([Installation guide](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html))

## Playbooks and Roles for IBM MQ installation

The playbooks and roles in this collection carryout an installation of IBM MQ Advanced on an Ubuntu target machine. The roles have been implemented to set up the required users on the machine, download the software, install and configure IBM MQ, copy over a configurable `dev-config.mqsc` file ready to be run on the target machine, and setup and start the web console. Developers can change this file to customise the configuration of their queue managers. Here we use a playbook that calls other playbooks but you can run the roles in playbooks to suit your requirements.

### Example Playbooks

ibmmq.yml - this playbook calls the mq-install and mq-setup playbooks, host names are passed into the imported playbook variable as {{ ansible_play_batch }}

```yaml
- name: Install and setup IBM MQ
  hosts: ['servers']

- name: Run the install playbook
  import_playbook: mq-install.yml

- name: Run the setup playbook
  import_playbook: mq-setup.yml
```

mq-install.yml - this playbook installs IBM MQ with the SSH user specified in the inventory
##### *Note*: The MQ *version* and app user's *UID and GID* can be specified here.
```yaml
- hosts: "{{ ansible_play_batch }}"
  serial: 1
  become: false
  environment:
    PATH: /opt/mqm/bin:{{ ansible_env.PATH }}

  roles:
    - role: setupusers
      vars:
        appUid: 909
        appGid: 909
        mqmHome: /home/mqm
        mqmProfile: .profile
    - role: downloadmq
      vars:
        version: 930
```
mq-setup.yml - this playbook sets up IBM MQ using the 'mqm' user

```yaml
- hosts: "{{ ansible_play_hosts }}"
  serial: 1
  become: yes
  become_user: mqm
  environment:
    PATH: /opt/mqm/bin:{{ ansible_env.PATH }}

  roles:
    - getconfig
    - setupconsole
    - startconsole

  tasks:

    - name: Create a queue manager
      queue_manager:
        qmname:
        - 'QM1'
        - 'QM2'
        state: 'present'
```