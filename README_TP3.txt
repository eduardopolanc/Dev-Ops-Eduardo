3-1 Document your inventory and base commands

all:
 vars:
   ansible_user: admin
   ansible_ssh_private_key_file: ~/.ssh/id_rsa
 children:
   prod:
     hosts: eduardoantonio.polancoarrindell.takima.cloud

3-2 Document your playbook

