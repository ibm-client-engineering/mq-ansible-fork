---
id: validate
sidebar_position: 1
title: Validate
---

# Troubleshooting

If one of the following errors appears during the run of the playbook, run the following commands according to the problem:

- `Please add this host's fingerprint to your known_hosts file to manage this host.` - Indicates that an SSH password cannot be used instead of a key. 
  
  Fix:
  ##### *Note*: change `[YOUR_HOST]` to the target machine's network address
  ```shell
  ssh-keyscan -H [YOUR_HOST] >> ~/.ssh/known_hosts
  ```
- `zsh: command not found: dspmq` - Appears that MQ environment variables have not been set.

  Fix:
  ```shell
  . /opt/mqm/bin/setmqenv -s
  ```
- `AMQ7077E: You are not authorized to perform the requested operation` - Appears that the user cannot carry out queue manager operations. This occurs when an SSH session to a target machine hasn't been refreshed after the roles have been executed.
  
  Fix:

  Restart the SSH session.


# Testing framework

### Testing module's functionality with playbooks

These playbooks test the functionality and performance of our roles and the queue_manager module in Ansible plays.

To run the test playbooks first:

1. copy your `inventory.ini` file to the `tests/playbooks` directory 
    ```shell
     cp invenotry.ini tests/playbooks
    ```
2. go to the `tests/playbooks` directory 
    ```shell
     cd tests/playbooks
    ```
3. export the modules to your Ansible library
    ```shell
     export ANSIBLE_LIBRARY=${ANSIBLE_LIBRARY}:<PATH-TO>/ansible_mq/ansible_collections/ibm/ibmmq/library
    ```
   - ##### *Note*: change `<PATH-TO>` to your local directory path:
4. run all test playbooks with `python3 main.py`