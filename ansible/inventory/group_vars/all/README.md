root@ansible-control:/ansible# chmod 400 .vault_password
root@ansible-control:/ansible# ansible-vault encrypt inventory/group_vars/all/vault.yml
Encryption successful