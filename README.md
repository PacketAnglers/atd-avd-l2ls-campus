# cleveland-atd-avd
Repo for Cleveland ATD 11-02-2022

![Topo](images/ATD-Campus-Topo.png)

## AVD Installation
Open Terminal in VScode and run the following installation script.  This will prep your host with AVD and install module requirements.
``` bash
bash -c "$(curl http://www.packetanglers.com/installavd.sh)"
```

## Update Password

In the group_vars/ATD.yml file, update the `ansible_password:` with your lab's unique password on line 5.

``` yaml
---  

# Credentials for CVP and EOS Switches
ansible_user: arista
ansible_password: XXXXXXXXXXX # Update password with your lab's password
ansible_network_os: arista.eos.eos
```

## Build Configs

The below ansible-play will build ther AVD generated configurations and store them in a local directory `intended/configs`.  

``` bash
ansible-playbook playbooks/build.yml
```

## Deploy Configs to your Lab Fabric

``` bash
ansible-playbook playbooks/deploy.yml
```
