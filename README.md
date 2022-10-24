# Cleveland ATD - 11/02/2022

The following repo builds an L2LS Campus fabric and deploys the configurations on Data Center (S1) on the Dual DC ATD Lab.

![Topo](images/ATD-Campus-Topo.svg)

## STEP #1 - Install AVD and Modules

Open Terminal in VScode and run the following installation script.  This will prep your host with AVD and install module requirements.

``` bash
bash -c "$(curl http://www.packetanglers.com/installavd.sh)"
```

## STEP #2 - Update Password

In the group_vars/ATD.yml file, update the `ansible_password:` with your lab's unique password on line 5.

``` yaml
---

# Credentials for CVP and EOS Switches
ansible_user: arista
ansible_password: XXXXXXXXXXX # Update password with your lab's password
ansible_network_os: arista.eos.eos
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
