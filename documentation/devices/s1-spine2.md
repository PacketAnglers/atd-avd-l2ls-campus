# s1-spine2
# Table of Contents

- [Management](#management)
  - [Management Interfaces](#management-interfaces)
  - [DNS Domain](#dns-domain)
  - [NTP](#ntp)
  - [Management API HTTP](#management-api-http)
- [Authentication](#authentication)
  - [Local Users](#local-users)
  - [RADIUS Servers](#radius-servers)
  - [IP RADIUS Source Interfaces](#ip-radius-source-interfaces)
  - [AAA Server Groups](#aaa-server-groups)
  - [AAA Authentication](#aaa-authentication)
  - [AAA Authorization](#aaa-authorization)
- [Monitoring](#monitoring)
  - [TerminAttr Daemon](#terminattr-daemon)
- [MLAG](#mlag)
  - [MLAG Summary](#mlag-summary)
  - [MLAG Device Configuration](#mlag-device-configuration)
- [Spanning Tree](#spanning-tree)
  - [Spanning Tree Summary](#spanning-tree-summary)
  - [Spanning Tree Device Configuration](#spanning-tree-device-configuration)
- [Internal VLAN Allocation Policy](#internal-vlan-allocation-policy)
  - [Internal VLAN Allocation Policy Summary](#internal-vlan-allocation-policy-summary)
  - [Internal VLAN Allocation Policy Configuration](#internal-vlan-allocation-policy-configuration)
- [VLANs](#vlans)
  - [VLANs Summary](#vlans-summary)
  - [VLANs Device Configuration](#vlans-device-configuration)
- [Interfaces](#interfaces)
  - [Ethernet Interfaces](#ethernet-interfaces)
  - [Port-Channel Interfaces](#port-channel-interfaces)
  - [Loopback Interfaces](#loopback-interfaces)
  - [VLAN Interfaces](#vlan-interfaces)
- [Routing](#routing)
  - [Service Routing Protocols Model](#service-routing-protocols-model)
  - [Virtual Router MAC Address](#virtual-router-mac-address)
  - [IP Routing](#ip-routing)
  - [IPv6 Routing](#ipv6-routing)
  - [Static Routes](#static-routes)
- [Multicast](#multicast)
  - [IP IGMP Snooping](#ip-igmp-snooping)
- [Filters](#filters)
- [ACL](#acl)
- [VRF Instances](#vrf-instances)
  - [VRF Instances Summary](#vrf-instances-summary)
  - [VRF Instances Device Configuration](#vrf-instances-device-configuration)
- [Quality Of Service](#quality-of-service)

# Management

## Management Interfaces

### Management Interfaces Summary

#### IPv4

| Management Interface | description | Type | VRF | IP Address | Gateway |
| -------------------- | ----------- | ---- | --- | ---------- | ------- |
| Management0 | oob_management | oob | default | 192.168.0.11/24 | 192.168.0.1 |

#### IPv6

| Management Interface | description | Type | VRF | IPv6 Address | IPv6 Gateway |
| -------------------- | ----------- | ---- | --- | ------------ | ------------ |
| Management0 | oob_management | oob | default | -  | - |

### Management Interfaces Device Configuration

```eos
!
interface Management0
   description oob_management
   no shutdown
   ip address 192.168.0.11/24
```

## DNS Domain

### DNS domain: atd.lab

### DNS Domain Device Configuration

```eos
dns domain atd.lab
!
```

## NTP

### NTP Summary

#### NTP Servers

| Server | VRF | Preferred | Burst | iBurst | Version | Min Poll | Max Poll | Local-interface | Key |
| ------ | --- | --------- | ----- | ------ | ------- | -------- | -------- | --------------- | --- |
| 192.168.0.1 | - | - | - | True | - | - | - | Management0 | - |

### NTP Device Configuration

```eos
!
ntp server 192.168.0.1 iburst local-interface Management0
```

## Management API HTTP

### Management API HTTP Summary

| HTTP | HTTPS | Default Services |
| ---- | ----- | ---------------- |
| False | True | - |

### Management API VRF Access

| VRF Name | IPv4 ACL | IPv6 ACL |
| -------- | -------- | -------- |
| default | - | - |

### Management API HTTP Configuration

```eos
!
management api http-commands
   protocol https
   no shutdown
   !
   vrf default
      no shutdown
```

# Authentication

## Local Users

### Local Users Summary

| User | Privilege | Role |
| ---- | --------- | ---- |
| arista | 15 | network-admin |

### Local Users Device Configuration

```eos
!
username arista privilege 15 role network-admin secret sha512 $6$QKglMNH1byQBb1RZ$CpICxVdZMgGypU88R9rBXYvobnp9NithP3ekBz.y6W6h6nIyBDKY.MKRJPiRLgEsYqoCI1g2SxorD9mQaGFKc/
username arista ssh-key ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDw05IMB87NmRYiVQZi5kr6Lqm4fyVMkWpRj3eh7iSiEMckeTuF9DLQtIHLOvGWt7R+3WJmsfTJwkm/yDql0tOUda9f5RPr0/CY97xwWipGbqtRW0Tqp8EhkWkpGJL+DUcrczAChovomWFj2PUpq+sjNAVzQEYtkN9ZIF58WwkYYW4AeApIq/AyS0N5ET5t4g9hUYwOcRDlJdykWDfdzdKZV3e4hKi+HejHFS3qnKDKeHavLfOxlSG/PQrL7guAqnH4NOdm9TjJ9l9R0K8MBE3iPLTcMQm5Ek+pDfRiCjhcTyd5XWkR3Rl/tFqiB+Qis/WA31sJTXqgVKodn+vVekUh arista@cleveland-atd-avd-1-30e03f6d
```

## RADIUS Servers

### RADIUS Servers

| VRF | RADIUS Servers |
| --- | ---------------|
| default | 192.168.0.1 |

### RADIUS Servers Device Configuration

```eos
!
radius-server host 192.168.0.1 key 7 0207165218120E
```

## IP RADIUS Source Interfaces

### IP RADIUS Source Interfaces

| VRF | Source Interface Name |
| --- | --------------- |
| default | Management0 |

### IP SOURCE Source Interfaces Device Configuration

```eos
!
ip radius source-interface Management0
```

## AAA Server Groups

### AAA Server Groups Summary

| Server Group Name | Type  | VRF | IP address |
| ------------------| ----- | --- | ---------- |
| atds | radius | default | 192.168.0.1 |

### AAA Server Groups Device Configuration

```eos
!
aaa group server radius atds
   server 192.168.0.1
```

## AAA Authentication

### AAA Authentication Summary

| Type | Sub-type | User Stores |
| ---- | -------- | ---------- |
| Login | default | group atds local |

### AAA Authentication Device Configuration

```eos
aaa authentication login default group atds local
!
```

## AAA Authorization

### AAA Authorization Summary

| Type | User Stores |
| ---- | ----------- |
| Exec | group atds local |

Authorization for configuration commands is disabled.

### AAA Authorization Privilege Levels Summary

| Privilege Level | User Stores |
| --------------- | ----------- |
| all | local |

### AAA Authorization Device Configuration

```eos
aaa authorization exec default group atds local
aaa authorization commands all default local
!
```

# Monitoring

## TerminAttr Daemon

### TerminAttr Daemon Summary

| CV Compression | CloudVision Servers | VRF | Authentication | Smash Excludes | Ingest Exclude | Bypass AAA |
| -------------- | ------------------- | --- | -------------- | -------------- | -------------- | ---------- |
| gzip | 192.168.0.5:9910 | - | key,atd-lab | ale,flexCounter,hardware,kni,pulse,strata | /Sysdb/cell/1/agent,/Sysdb/cell/2/agent | False |

### TerminAttr Daemon Device Configuration

```eos
!
daemon TerminAttr
   exec /usr/bin/TerminAttr -cvaddr=192.168.0.5:9910 -cvauth=key,atd-lab -smashexcludes=ale,flexCounter,hardware,kni,pulse,strata -ingestexclude=/Sysdb/cell/1/agent,/Sysdb/cell/2/agent -taillogs
   no shutdown
```

# MLAG

## MLAG Summary

| Domain-id | Local-interface | Peer-address | Peer-link |
| --------- | --------------- | ------------ | --------- |
| SPINES | Vlan4094 | 10.0.0.0 | Port-Channel1 |

Dual primary detection is disabled.

## MLAG Device Configuration

```eos
!
mlag configuration
   domain-id SPINES
   local-interface Vlan4094
   peer-address 10.0.0.0
   peer-link Port-Channel1
   reload-delay mlag 300
   reload-delay non-mlag 330
```

# Spanning Tree

## Spanning Tree Summary

STP mode: **mstp**

### MSTP Instance and Priority

| Instance(s) | Priority |
| -------- | -------- |
| 0 | 4096 |

### Global Spanning-Tree Settings

- Spanning Tree disabled for VLANs: **4093-4094**

## Spanning Tree Device Configuration

```eos
!
spanning-tree mode mstp
no spanning-tree vlan-id 4093-4094
spanning-tree mst 0 priority 4096
```

# Internal VLAN Allocation Policy

## Internal VLAN Allocation Policy Summary

| Policy Allocation | Range Beginning | Range Ending |
| ------------------| --------------- | ------------ |
| ascending | 1006 | 1199 |

## Internal VLAN Allocation Policy Configuration

```eos
!
vlan internal order ascending range 1006 1199
```

# VLANs

## VLANs Summary

| VLAN ID | Name | Trunk Groups |
| ------- | ---- | ------------ |
| 112 | Host_Network_112 | - |
| 134 | Host_Network_134 | - |
| 4093 | LEAF_PEER_L3 | LEAF_PEER_L3 |
| 4094 | MLAG_PEER | MLAG |

## VLANs Device Configuration

```eos
!
vlan 112
   name Host_Network_112
!
vlan 134
   name Host_Network_134
!
vlan 4093
   name LEAF_PEER_L3
   trunk group LEAF_PEER_L3
!
vlan 4094
   name MLAG_PEER
   trunk group MLAG
```

# Interfaces

## Ethernet Interfaces

### Ethernet Interfaces Summary

#### L2

| Interface | Description | Mode | VLANs | Native VLAN | Trunk Group | Channel-Group |
| --------- | ----------- | ---- | ----- | ----------- | ----------- | ------------- |
| Ethernet1 | MLAG_PEER_s1-spine1_Ethernet1 | *trunk | *2-4094 | *- | *['LEAF_PEER_L3', 'MLAG'] | 1 |
| Ethernet2 | S1-LEAF1_Ethernet3 | *trunk | *112,134 | *- | *- | 2 |
| Ethernet3 | S1-LEAF2_Ethernet3 | *trunk | *112,134 | *- | *- | 2 |
| Ethernet4 | S1-LEAF3_Ethernet3 | *trunk | *112,134 | *- | *- | 4 |
| Ethernet5 | S1-LEAF4_Ethernet3 | *trunk | *112,134 | *- | *- | 4 |
| Ethernet6 | MLAG_PEER_s1-spine1_Ethernet6 | *trunk | *2-4094 | *- | *['LEAF_PEER_L3', 'MLAG'] | 1 |

*Inherited from Port-Channel Interface

### Ethernet Interfaces Device Configuration

```eos
!
interface Ethernet1
   description MLAG_PEER_s1-spine1_Ethernet1
   no shutdown
   channel-group 1 mode active
!
interface Ethernet2
   description S1-LEAF1_Ethernet3
   no shutdown
   channel-group 2 mode active
!
interface Ethernet3
   description S1-LEAF2_Ethernet3
   no shutdown
   channel-group 2 mode active
!
interface Ethernet4
   description S1-LEAF3_Ethernet3
   no shutdown
   channel-group 4 mode active
!
interface Ethernet5
   description S1-LEAF4_Ethernet3
   no shutdown
   channel-group 4 mode active
!
interface Ethernet6
   description MLAG_PEER_s1-spine1_Ethernet6
   no shutdown
   channel-group 1 mode active
```

## Port-Channel Interfaces

### Port-Channel Interfaces Summary

#### L2

| Interface | Description | Type | Mode | VLANs | Native VLAN | Trunk Group | LACP Fallback Timeout | LACP Fallback Mode | MLAG ID | EVPN ESI |
| --------- | ----------- | ---- | ---- | ----- | ----------- | ------------| --------------------- | ------------------ | ------- | -------- |
| Port-Channel1 | MLAG_PEER_s1-spine1_Po1 | switched | trunk | 2-4094 | - | ['LEAF_PEER_L3', 'MLAG'] | - | - | - | - |
| Port-Channel2 | IDF1_Po2 | switched | trunk | 112,134 | - | - | - | - | 2 | - |
| Port-Channel4 | IDF2_Po2 | switched | trunk | 112,134 | - | - | - | - | 4 | - |

### Port-Channel Interfaces Device Configuration

```eos
!
interface Port-Channel1
   description MLAG_PEER_s1-spine1_Po1
   no shutdown
   switchport
   switchport trunk allowed vlan 2-4094
   switchport mode trunk
   switchport trunk group LEAF_PEER_L3
   switchport trunk group MLAG
!
interface Port-Channel2
   description IDF1_Po2
   no shutdown
   switchport
   switchport trunk allowed vlan 112,134
   switchport mode trunk
   mlag 2
!
interface Port-Channel4
   description IDF2_Po2
   no shutdown
   switchport
   switchport trunk allowed vlan 112,134
   switchport mode trunk
   mlag 4
```

## Loopback Interfaces

### Loopback Interfaces Summary

#### IPv4

| Interface | Description | VRF | IP Address |
| --------- | ----------- | --- | ---------- |
| Loopback0 | - | default | 1.1.1.2/32 |

#### IPv6

| Interface | Description | VRF | IPv6 Address |
| --------- | ----------- | --- | ------------ |
| Loopback0 | - | default | - |


### Loopback Interfaces Device Configuration

```eos
!
interface Loopback0
   no shutdown
   ip address 1.1.1.2/32
```

## VLAN Interfaces

### VLAN Interfaces Summary

| Interface | Description | VRF |  MTU | Shutdown |
| --------- | ----------- | --- | ---- | -------- |
| Vlan112 | Host_Network_112 | default | - | False |
| Vlan134 | Host_Network_134 | default | - | False |
| Vlan4093 | MLAG_PEER_L3_PEERING | default | 9000 | False |
| Vlan4094 | MLAG_PEER | default | 9000 | False |

#### IPv4

| Interface | VRF | IP Address | IP Address Virtual | IP Router Virtual Address | VRRP | ACL In | ACL Out |
| --------- | --- | ---------- | ------------------ | ------------------------- | ---- | ------ | ------- |
| Vlan112 |  default  |  10.111.112.3/24  |  -  |  10.111.112.1  |  -  |  -  |  -  |
| Vlan134 |  default  |  10.111.134.3/24  |  -  |  10.111.134.1  |  -  |  -  |  -  |
| Vlan4093 |  default  |  10.1.1.1/31  |  -  |  -  |  -  |  -  |  -  |
| Vlan4094 |  default  |  10.0.0.1/31  |  -  |  -  |  -  |  -  |  -  |

### VLAN Interfaces Device Configuration

```eos
!
interface Vlan112
   description Host_Network_112
   no shutdown
   ip address 10.111.112.3/24
   ip virtual-router address 10.111.112.1
!
interface Vlan134
   description Host_Network_134
   no shutdown
   ip address 10.111.134.3/24
   ip virtual-router address 10.111.134.1
!
interface Vlan4093
   description MLAG_PEER_L3_PEERING
   no shutdown
   mtu 9000
   ip address 10.1.1.1/31
!
interface Vlan4094
   description MLAG_PEER
   no shutdown
   mtu 9000
   no autostate
   ip address 10.0.0.1/31
```

# Routing
## Service Routing Protocols Model

Multi agent routing protocol model enabled

```eos
!
service routing protocols model multi-agent
```

## Virtual Router MAC Address

### Virtual Router MAC Address Summary

#### Virtual Router MAC Address: aa:aa:bb:bb:cc:cc

### Virtual Router MAC Address Configuration

```eos
!
ip virtual-router mac-address aa:aa:bb:bb:cc:cc
```

## IP Routing

### IP Routing Summary

| VRF | Routing Enabled |
| --- | --------------- |
| default | True |
| default | false |

### IP Routing Device Configuration

```eos
!
ip routing
```
## IPv6 Routing

### IPv6 Routing Summary

| VRF | Routing Enabled |
| --- | --------------- |
| default | False |
| default | false |

## Static Routes

### Static Routes Summary

| VRF | Destination Prefix | Next Hop IP             | Exit interface      | Administrative Distance       | Tag               | Route Name                    | Metric         |
| --- | ------------------ | ----------------------- | ------------------- | ----------------------------- | ----------------- | ----------------------------- | -------------- |
| default | 0.0.0.0/0 | 192.168.0.1 | - | 1 | - | - | - |

### Static Routes Device Configuration

```eos
!
ip route 0.0.0.0/0 192.168.0.1
```

# Multicast

## IP IGMP Snooping

### IP IGMP Snooping Summary

| IGMP Snooping | Fast Leave | Interface Restart Query | Proxy | Restart Query Interval | Robustness Variable |
| ------------- | ---------- | ----------------------- | ----- | ---------------------- | ------------------- |
| Enabled | - | - | - | - | - |

### IP IGMP Snooping Device Configuration

```eos
```

# Filters

# ACL

# VRF Instances

## VRF Instances Summary

| VRF Name | IP Routing |
| -------- | ---------- |
| default | disabled |

## VRF Instances Device Configuration

```eos
```

# Quality Of Service
