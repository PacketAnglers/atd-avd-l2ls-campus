# Cleveland ATD - 11/02/2022

This repository builds a L2LS Campus fabric onto the Dual Data Center ATD Lab. The below diagram respresents a single DC within the larger Lab topology. Using AVD, we will build and deploy configurations for an example Campus Fabric.

![Topo](images/ATD-Campus-Topo.svg)

## Requirements

- Start the Dual Data Center ATD Lab

<img src="images/running-dual-dc-lab.png" alt="folder" width="400"/>

## STEP #1 - Launch Programmability IDE

Launch the Progammability IDE (lefthand column of Lab Topology).  If this is the first time starting the IDE you will be prompted for a code-server password.  Your unique password is noted on the Lab Topology page.

<img src="images/code-server.png" alt="folder" width="400"/>

Click through any pop-ups that may occur.

Now, start a new terminal session by clicking on the hamburger and selecting Terminal->New Terminal.

![Topo](images/programmability_ide.png)

## STEP #2 - Install AVD

From the terminal session, run the following installation script. This will prep your host with AVD and install module requirements.

``` bash
bash -c "$(curl http://www.packetanglers.com/installavd.sh)"
```

## STEP #3 - Change Working Directory

Change to the following directory. All commands will be executed from here.

``` bash
cd labfiles/cleveland-atd-avd
```

## STEP #4 - Update Passwords and SSH Keys

From the Programmibility IDE Explorer:

- Navigate to the `labfiles/cleveland-atd-avd/group_vars` folder.
- Click on the **group_vars/ATD.yml** file to open an editor tab
- Update the following **three** variables.
  - line 5 - `ansible_password:` replace with your Lab's unique password
  - line 49 - `sha512_password:` retrieve from switch
  - line 50 - `ssh_key:` retrieve from switch

The `ansible_password` key (line 5) is used by Ansible to log into your switches to deploy configurations. The password is unique to your Lab instance and can be found from the **Usernames and Passwords** section of your lab topology screen.

<img src="images/username_passwords.png" alt="username_passwords" width="500"/>

``` yaml
---

# Credentials for CVP and EOS Switches
ansible_user: arista
ansible_password: XXXXXXXXXXX # Update password with your Lab's password
ansible_network_os: arista.eos.eos
```

_Now the tricky part lines 49 & 50...._  The current `arista` username password on the switches is an encrypted type 5 password.  We need to convert this password to a sha512 version. This can be accomplished by logging on to one of your switches and running the following commands.  _**Make sure to update `XXXXXXXX` with your Lab's unique password.**_

``` bash
config
username arista privilege 15 role network-admin secret XXXXXXXX
```

Now we can display and copy/paste the sha512 password and ssh key for username `arista` by typing the following command.

``` bash
show run section username | grep arista
```

Output:

``` bash
username arista privilege 15 role network-admin secret sha512 $6$ebPETJmTzMXalZW0$7zyBIqsR/yjRh2LVL45dFLS5YSEGLfmrnnZtBNcaXW1YncuNWI6UMhk2wOmalqhSL/lFNhMpKhXnY.ztYXtQ31
username arista ssh-key ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDw05IMB87NmRYiVQZi5kr6Lqm4fyVMkWpRj3eh7iSiEMckeTuF9DLQtIHLOvGWt7R+3WJmsfTJwkm/yDql0tOUda9f5RPr0/CY97xwWipGbqtRW0Tqp8EhkWkpGJL+DUcrczAChovomWFj2PUpq+sjNAVzQEYtkN9ZIF58WwkYYW4AeApIq/AyS0N5ET5t4g9hUYwOcRDlJdykWDfdzdKZV3e4hKi+HejHFS3qnKDKeHavLfOxlSG/PQrL7guAqnH4NOdm9TjJ9l9R0K8MBE3iPLTcMQm5Ek+pDfRiCjhcTyd5XWkR3Rl/tFqiB+Qis/WA31sJTXqgVKodn+vVekUh arista@cleveland-atd-avd-1-30e03f6d
```

Now update sha512_password and ssh_key with these values. _Remember to keep the double quotes and DO NOT REMOVE `ssh-rsa` from the ssh_key variable._

- line 49 - `sha512_password:`
- line 50 - `ssh_key:`

You file should look similar to below.  DO NOT USE these values, but rather the ones from your show command output, as they are unique to your switches.

``` yaml
# local users
local_users:
  arista:
    privilege: 15
    role: network-admin
    # Update sha512_password and ssh_key from one of your Lab switches
    sha512_password: "$6$ebPETJmTzMXalZW0$7zyBIqsR/yjRh2LVL45dFLS5YSEGLfmrnnZtBNcaXW1YncuNWI6UMhk2wOmalqhSL/lFNhMpKhXnY.ztYXtQ31"
    ssh_key: "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDw05IMB87NmRYiVQZi5kr6Lqm4fyVMkWpRj3eh7iSiEMckeTuF9DLQtIHLOvGWt7R+3WJmsfTJwkm/yDql0tOUda9f5RPr0/CY97xwWipGbqtRW0Tqp8EhkWkpGJL+DUcrczAChovomWFj2PUpq+sjNAVzQEYtkN9ZIF58WwkYYW4AeApIq/AyS0N5ET5t4g9hUYwOcRDlJdykWDfdzdKZV3e4hKi+HejHFS3qnKDKeHavLfOxlSG/PQrL7guAqnH4NOdm9TjJ9l9R0K8MBE3iPLTcMQm5Ek+pDfRiCjhcTyd5XWkR3Rl/tFqiB+Qis/WA31sJTXqgVKodn+vVekUh arista@cleveland-atd-avd-1-30e03f6d"
```

## STEP #5 - Build Configs

From the terminal window, run the command below to execute an ansible playbook and build the AVD generated configurations and store them in a local directory `intended/configs`.

``` bash
make build
```

## STEP #6 - Deploy Configs to your Lab Fabric

The command below will deploy your configurations to your switches. This playbook uses Arista's eAPI & eos_config module to do a config replacement of the switches running_config.

``` bash
make deploy
```

## STEP #7 - Test Traffic from Host1 to Host2

Connect to `s1-host` and ping `s1-host2`

``` bash
ping 10.20.20.101

PING 10.20.20.101 (10.20.20.101) 72(100) bytes of data.
80 bytes from 10.20.20.101: icmp_seq=1 ttl=63 time=34.0 ms
80 bytes from 10.20.20.101: icmp_seq=2 ttl=63 time=30.2 ms
80 bytes from 10.20.20.101: icmp_seq=3 ttl=63 time=25.2 ms
80 bytes from 10.20.20.101: icmp_seq=4 ttl=63 time=21.1 ms
80 bytes from 10.20.20.101: icmp_seq=5 ttl=63 time=23.0 ms
```

## Network Ports and 802.1x Port Profiles

So far, the above example has shown how to build and deploy configurations using AVD. In a Campus environment, we typically configure a range of ports within a leaf switch to have the same charactestics (vlan, mode, portfast, NAC, etc...).  AVD provides a way to define a **Port Profile** and then apply to a range of ports across multiple switches.  For example, suppose we wish to configure port range Ethernet7-48 on leafs s1-leaf1 and s1-leaf2 with the following configuration.  Documentation for this feature can be found [here](https://avd.sh/en/devel/roles/eos_designs/doc/connected-endpoints.html#network-ports_1).

``` bash
interface EthernetX
   switchport trunk native vlan 10
   switchport phone vlan 15
   switchport phone trunk untagged
   switchport mode trunk phone
   switchport
   dot1x pae authenticator
   dot1x authentication failure action traffic allow vlan 999
   dot1x reauthentication
   dot1x port-control auto
   dot1x host-mode multi-host authenticated
   dot1x mac based authentication
   dot1x timeout tx-period 3
   dot1x timeout reauth-period server
   dot1x reauthorization request limit 3
   spanning-tree portfast
   spanning-tree bpduguard enable
```

Open up file **group_vars/ATD_FABRIC_PORTS.yml**  and uncomment lines 25-57. Snippet from the file below.

``` yaml
network_ports:

# ---------------- s1-leaf1/2 ----------------

  - switches:
      - s1-leaf[12] # regex match s1-leaf1 & s1-leaF2
    switch_ports:
      - Ethernet7-48
    profile: PP-DOT1X
    native_vlan: 10
    structured_config:
      phone:
        trunk: untagged
        vlan: 15
    dot1x:
      authentication_failure:
        action: allow
        allow_vlan: 999
```

On Mac:

- Hightlight lines 25-57 under network_ports and press (CMD + /).

On Windows:

- Hightlight lines 25-57 under network_ports and press (Ctrl + /).

For this Lab, we can simulate building these configurations, but will not be able to deploy them because the virtual switches do not include these interfaces.

Now that network_ports has been defined to use a port_profile called `PP-DOT1X` you can build the configurations again and preview them in **intended/configs**.

``` bash
make build
```

## Next steps

Try modifying the network_ports keys to include other ports and switches...
