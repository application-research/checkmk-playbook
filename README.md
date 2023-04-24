
# CheckMK Playbook

A playbook to install a CheckMK server and to connect clients.

## Install prerequisite packages
`$ ansible-galaxy collection install community.crypto`

## Running this playbook

`# ansible-playbook -v -i ./inventories/pchq server.yml`