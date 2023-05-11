
# CheckMK Playbook

A playbook to install a CheckMK server and to connect clients.


## Install prerequisite packages
```
$ ansible-galaxy collection install community.crypto
$ ansible-galaxy collection install tribe29.checkmk
```


## Deploy a CheckMK server:

First fill out some of the group_vars in your own inventory:

`$ nano ./inventories/pchq/group_vars.yml`

Then run the server.yml playbook against that inventory:

`$ ansible-playbook -v -i ./inventories/pchq server.yml`

If it's the first time running this playbook observe the administrator password that's generated in the playbooks output.

You then need to use the webgui to create a user with the 'automation' role.

1) In CheckMK, go 'Setup' > 'Users' > Select the pencil icon next to the 'automation' user to edit it.

2) Click the green dice logo to generate a new automation secret.

3) Record it as the {{ check_automation_secret }} variable in group_vars.yml

4) Click 'Save' to apply the newly generated automation secret.


## Connect each CheckMK agent:

First edit a hosts file in your own inventory that includes each target server in the `[checkmk_clients]` group:

`$ nano ./inventories/pchq/hosts`

Then run the clients.yml playbook against that inventory:

`$ ansible-playbook -v -i ./inventories/pchq clients.yml`
