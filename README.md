# Cleveland ATD - 11/02/2022

The following repo builds an L2LS Campus fabric and deploys the configurations on Data Center (S1) on the Dual DC ATD Lab.

![Topo](images/ATD-Campus-Topo.svg)

## STEP #1 - Install AVD and Modules

Open Terminal in VScode and run the following installation script.  This will prep your host with AVD and install module requirements.

``` bash
bash -c "$(curl http://www.packetanglers.com/installavd.sh)"
```

## STEP #2 - Update Passwords and SSH Keys

In the **group_vars/ATD.yml** file, update the following **three** variables:

- line 5 - `ansible_password:` with your Lab's unique password
- line 49 - `sha512_password:` from a switch
- line 50 - `ssh_key:` from a switch

The `ansible_password` variable is used by Ansible to log into your switches to deploy configurations.  The password is unique to your Lab instance and can be found from the **Usernames and Passwords** section of your lab topology screen.

<p style="border: 10px solid;>
<img src="images/username_passwords.png" alt="username_passwords" width="400"/>
</p>

``` yaml
---

# Credentials for CVP and EOS Switches
ansible_user: arista
ansible_password: XXXXXXXXXXX # Update password with your Lab's password
ansible_network_os: arista.eos.eos
```

Update the switches local `arista` user with a sha512 password by typing to the following on one of your Lab switches.

``` bash
config
username arista privilege 15 role network-admin secret XXXXXXXX # your unique Lab password
```

Now we can display the sha512 password and ssh key.

``` bash
sh run section username
username admin privilege 15 role network-admin secret 5 $1$5O85YVVn$HrXcfOivJEnISTMb6xrJc.
username arista privilege 15 role network-admin secret sha512 $6$ebPETJmTzMXalZW0$7zyBIqsR/yjRh2LVL45dFLS5YSEGLfmrnnZtBNcaXW1YncuNWI6UMhk2wOmalqhSL/lFNhMpKhXnY.ztYXtQ31
username arista ssh-key ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDw05IMB87NmRYiVQZi5kr6Lqm4fyVMkWpRj3eh7iSiEMckeTuF9DLQtIHLOvGWt7R+3WJmsfTJwkm/yDql0tOUda9f5RPr0/CY97xwWipGbqtRW0Tqp8EhkWkpGJL+DUcrczAChovomWFj2PUpq+sjNAVzQEYtkN9ZIF58WwkYYW4AeApIq/AyS0N5ET5t4g9hUYwOcRDlJdykWDfdzdKZV3e4hKi+HejHFS3qnKDKeHavLfOxlSG/PQrL7guAqnH4NOdm9TjJ9l9R0K8MBE3iPLTcMQm5Ek+pDfRiCjhcTyd5XWkR3Rl/tFqiB+Qis/WA31sJTXqgVKodn+vVekUh arista@cleveland-atd-avd-1-30e03f6d
```

Now copy the sha512 password and ssh_key and update them below.  _Remember to keep the double quotes._

- line 49 - `sha512_password:`
- line 50 - `ssh_key:`

``` yaml
# local users
local_users:
  arista:
    privilege: 15
    role: network-admin
    # Update sha512_password and ssh_key from one of your Lab switches
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
