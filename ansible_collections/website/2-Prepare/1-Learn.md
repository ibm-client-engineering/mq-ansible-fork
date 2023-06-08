# Run our sample playbook

##### *Note*: *Ansible* must be installed on the local machine ([Installation guide](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html))

Before running the playbook and implementing our modules and roles for IBM MQ:

1. Check if you have an *ssh* key pair in order to access the target machines via SSH. Go to the `~/.ssh` directory in your machine and look for the public and private key files e.g. `id_rsa` and `id_rsa.pub`.

    ```shell
     cd ~/.ssh
    ```

2. If those two files are not in your `ssh` directory, you need to generate `id_rsa` and `id_rsa.pub` with the following command:

    ```shell
     ssh-keygen
    ```

3. Once the keys have been generated, you need to copy the public key to the target machine's user `ssh` directory.

    ```shell
     ssh-copy-id -i id_rsa.pub [USER]@[YOUR_TARGET_HOST]
    ```
    
4. To confirm the keys have been copied successfully, connect to your target machine by:

    ```shell
     ssh [USER]@[YOUR_TARGET_HOST]
    ```
    This should connect to your target machine without asking for a password.
    
5. On your local machine clone this repository. 

6. Go to the `ansible_collections/ibm/ibmmq/` directory.

    ```shell
     cd mq-ansible/ansible_collections/ibm/ibmmq/
    ```


7. Create a file `inventory.ini` inside the directory with the following content:
  
    ```ini
    
    [servers]
    YOUR_HOST_ALIAS ansible_host=YOUR_HOSTNAME ansible_ssh_user=YOUR_SSH_USER
    YOUR_HOST_ALIAS ansible_host=YOUR_HOSTNAME ansible_ssh_user=YOUR_SSH_USER

    ```
   ##### *Note*: you can specify one or more hosts
   - Change `YOUR_HOST_ALIAS` to an alias name that you wish to use e.g. `mq-host-1` , you can omit aliases if you prefer
   - Change `YOUR_HOSTNAME` to your server/hostname, e.g. `myserver-1.fyre.com`
   - Change `YOUR_SSH_USER` to your target machine's SSH user
   ##### *Note*: the user on the target machine MUST have `root` or `sudo` privileges

### ibmmq.yml

The sample playbook [`ibmmq.yml`](ansible_collections/ibm/ibmmq/ibmmq.yml) installs IBM MQ Advanced with our roles and configures a queue manager with the `queue_manager.py` module.

1. Before running the playbook, ensure that you have added the following directory path to the ANSIBLE_LIBRARY environment variable.

    ##### *Note*: change `<PATH-TO>` to your local directory path:

    - On Mac:

       ```shell
          export ANSIBLE_LIBRARY=${ANSIBLE_LIBRARY}:<PATH-TO>/mq-ansible/ansible_collections/ibm/ibmmq/library
       ```

    - On Windows:
    
      ```shell
          set ANSIBLE_LIBRARY=%ANSIBLE_LIBRARY%;<PATH-TO>/mq-ansible/ansible_collections/ibm/ibmmq/library
       ```

2. Run the following command to execute the tasks within the playbook:
      ```shell
       ansible-playbook ./ibmmq.yml -i inventory.ini -e 'ibmMqLicence=accept'
      ```
      - ##### *Note*: you can optionally add `-K` (uppercase) to the command, this will prompt the user to enter the sudo password for [YOUR_SSH_USER] on the target machine, you can omit if you have setup SSH keys

3. The playbook should return the result of `dspmq` with the queue manager created listed. Log into your target machine and check it manually:

    ```shell
     dspmq
    ```
