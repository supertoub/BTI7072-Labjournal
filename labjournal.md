# LAB Journal
## 19. February 2019
Tobias Weissert & Thomas Baumann 
- Set up Git repo
- Set up LAB-Journal
- Group assingment nr: n113
- Familiarize with the virtual lab setup
- Search RHEL 7 Networking guide (TODO) 
- Router VM edit config of ENS4 (TODO)

```
Router:
Update /etc/resolve.conf
search n113.nslab.ch nslab.ch
nameserver 193.5.80.80
```
<br/>

Router:
```
Update /etc/hostname
router.n113.nslab.ch
```

## 26. Februar 2019
Tobias Weissert & Thomas Baumann<br/>

Router:
```
Update file: /etc/sysconfig/network-scripts/ifcfg-ens4
DEVICE=ens4
NM_CONTROLLED=no
TYPE=Ethernet
ONBOOT=yes
BOOTPROTO=none
IPADDR=192.5.80.113
PREFIX=27
GATEWAY=192.5.80.1
IPV4_FAILURE_FATAL=yes
Name="System eth0"
```
<br/>

Router:
```
Update file: /etc/sysconfig/network-scripts/ifcfg-ens3
DEVICE=ens3
NM_CONTROLLED=no
TYPE=Ethernet
ONBOOT=yes
BOOTPROTO=none
IPADDR=192.5.82.129
PREFIX=27
GATEWAY=192.5.82.1
IPV4_FAILURE_FATAL=yes
Name="System eth0"
```
<br/>

Router:
```
Update /etc/sysctl.conf
Net.ipv4.ip_forward = 1

sysctl -p /etc/sysctl.conf
systemctl restart network
```
<br/>

Router:
```
ping 8.8.8.8 ✓
traceroute 8.8.8.8 ✓
ping google.com ✓
```
<br/>

Client:
- Set IP to manual: 193.5.82.128/27 Gateway: 193.5.82.129
- Set DNS Server to 192.5.80.80
