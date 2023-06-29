
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

Also observe the servers filename of the CheckMK agent for Debian/Ubuntu (eg: check-mk-agent_2.1.0p20-1_all.deb):
```
root@monitoring:~# ls /opt/omd/sites/estuary/share/check_mk/agents/
cfg_examples                          check_mk_agent.hpux     check_mk_agent.openvms        linux                    plugins  windows
check-mk-agent_2.1.0p20-1_all.deb     check_mk_agent.linux    check_mk_agent.openwrt        mk-job                   sap      z_os
check-mk-agent-2.1.0p20-1.noarch.rpm  check_mk_agent.macosx   check_mk_agent.solaris        mk-job.aix               scripts
check_mk_agent.aix                    check_mk_agent.netbsd   check_mk_caching_agent.linux  mk-job.solaris           special
check_mk_agent.freebsd                check_mk_agent.openbsd  CONTENTS                      mk-remote-alert-handler  waitmax
```


## Connecting CheckMK agents:

1) Edit a hosts file in your own inventory that includes each target server in the `[checkmk_clients]` group:

`$ nano ./inventories/pchq/hosts`

2) Get the full hash value for the specific client you wish to install:

[image]

Here we see the DEB link is: https://monitoring.estuary.tech/estuary/check_mk/download_agent.py?hash=025d6a48928e3af5&os=linux_deb

So the hash value we're looking for here is: "025d6a48928e3af5"


Then run the clients.yml playbook against that inventory:

`$ ansible-playbook -v -i ./inventories/pchq connect.yml --extra-vars "check_mk_agent_hash=025d6a48928e3af5"`


## Disconnecting CheckMK agents:

`$ ansible-playbook -v -i ./inventories/pchq disconnect.yml`

You then need to 'activate' these changes within the CheckMK GUI.


## Automatically Accepting Changes

It's recommended that after connecting clients with this script, you manually accept or reject the discovered services or other changes through the GUI.

If you want to just blindly accept all the newly connected/disconnected hosts though you can run this playbook with the 'activate-changes' tag:

`$ ansible-playbook -v -i ./inventories/pchq disconnect.yml --extra-vars "activate_changes=true"`

