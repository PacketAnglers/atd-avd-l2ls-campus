# Cleveland ATD - 11/02/2022

The following repo builds an L2LS Campus fabric and deploys the configurations on Data Center (S1) on the Dual DC ATD Lab.

![Topo](images/ATD-Campus-Topo.svg)

## STEP #1 - Install AVD and Modules

Open Terminal in VScode and run the following installation script.  This will prep your host with AVD and install module requirements.

``` bash
bash -c "$(curl http://www.packetanglers.com/installavd.sh)"
```

## STEP #2 - Update Passwords and SSH Keys

In the group_vars/ATD.yml file, update the following variables:

Ansible Credentails to log into your switches

- line 5 - `ansible_password:` with your Lab's unique password

Local User sha512 password and ssh key to configure on the switch

- line 49 - `sha512_password:`
- line 49 - `ssh_key:`

``` yaml
---

# Credentials for CVP and EOS Switches
ansible_user: arista
ansible_password: XXXXXXXXXXX # Update password with your lab's password
ansible_network_os: arista.eos.eos

... # truncated

# local users
local_users:
  arista:
    privilege: 15
    role: network-admin
    # Update sha512_password and ssh_key with your Lab's password and key
    sha512_password: "XXXXXXXXXXXXXXXXXXXXXXXXXX"
    ssh_key: "ssh-rsa XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"


```

## STEP #3 - Build Configs

The command below will run an ansible playbook and build the AVD generated configurations and store them in a local directory `intended/configs`.

``` bash
make build
```

## STEP #4 - Deploy Configs to your Lab Fabric

The command below will also build your configuration files and then deploy them on fabric nodes.

``` bash
make deploy
```

## STEP #5 - Test Traffic from Host1 to Host2
