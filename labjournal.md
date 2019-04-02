# LAB Journal Serie 1
## Exercise 1
### 19. February 2019
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

### 26. Februar 2019
Tobias Weissert & Thomas Baumann<br/>

Router:
```
Update file: /etc/sysconfig/network-scripts/ifcfg-ens4
DEVICE=ens4
NM_CONTROLLED=no
TYPE=Ethernet
ONBOOT=yes
BOOTPROTO=none
IPADDR=193.5.80.113
PREFIX=27
GATEWAY=193.5.80.1
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
IPADDR=193.5.82.129
PREFIX=27
GATEWAY=193.5.82.1
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
- Set DNS Server to 193.5.80.80

### 5. März 2019
Tobias Weissert & Thomas Baumann
Network capture
![Capture networkmonitor](./NetworkMonitorScreenshot.png)

ARP capture
![ARP Capture](./arpScreenshot.png)

## Exercise 2
Router:
```ping 193.5.82.100 [Redirect host, nexthop: 193.5.80.112]```
![Ping another group](./ICMPRedirect.png)

Router:
```ip route add 193.5.82.96/27 via 193.5.80.112 dev ens4```

Make route persistent create file /etc/sysconfig/network-scripts/route-ens4
```193.5.82.96/27 via 193.5.80.112 dev ens4```

## Exercise 3
Router: change /etc/sysconfig/network-scripts/ifcfg-ens3 and ifcfg-ens4
```
ONBOOT=no
```

Router: add to /etc/quagga/zebra.conf
```
log file /var/log/quagga/zebra.log
```

```
systemcpl start zebra
```
```
vtysh:
conf t
interface ens3
ip address 193.5.82.129/27
interface ens4
ip address 193.80.113/27
ip route 193.5.82.96/27 193.5.80.112
ip route 193.5.82.96/27 ens4
ip route 0.0.0.0/0 193.5.80.1
write mem
```

### 12. März 2019
Tobias Weissert & Thomas Baumann
```
vtysh:
conf t
no ip route 193.5.82.96/27 193.5.80.112
no ip route 193.5.82.96/27 ens4
no ip route 0.0.0.0/0 193.5.80.1
no ip address 193.5.82.129/27

ip address 193.5.82.129/24
ping 8.8.8.8 ✓
```

## Exercise 4
Router: add to /etc/quagga/ripd.conf
```
log file /etc/quagga/ripd.conf
```
```
systemctl start ripd
Log contains: RIPd starting
```
```
chown quagga.quagga /var/log/qzagga/ripd.conf
vtysh
no ip route 0.0.0.0/ 193.5.80.1
conf t key chain demonet
key 1
key-string demo$rip
interface ens4
ip rip authentication mode md5
ip rip authentication key-chain demonet

router rip
redistribute connected
network 193.5.80.0/24
network ens4
distance 100 193.5.80.0/24
ping 8.8.8.8 ✓
```
![RIP Configuration](./RIPConf.png)
![RIP Announcment](./RIPAnnouncement.png)

## Exercise 5
Router: add to /etc/quagga/ospfd.conf
```
log file /var/log/quagga/ospfd.conf
```
```
systemctl start ospfd
ospf starting
chown quagga.quagga /var/log/qzagga/ospfd.conf
```
```
vtysh coinf t
router ospf
ospf router-id 193.5.80.113
interface ens4
ip ospf authentication message-digest
ip ospf message-digest-key 1 md5 demo$ospf
redistribute connected
network 193.5.80.0/24 area 0.0.0.0
area 0.0.0.0 range 193.5.80.0/24
area 0.0.0.0 authentication message-digest
systemctl enable ospfd
```
![OSPF Capture](./ospfCapture.png)

## Exercise 6
### 19. März 2019
Tobias Weissert & Thomas Baumann

![Route priority](./ospfVSrip.png)
RIP has a higher priority

```
router rip
distance 120 193.5.82.160/27
ip route 192.5.80.1 0.0.0.0/0 130
```

![Static route](./staticRouteBackup.png)

### 26. März 2019 IETF 104
# LAB Journal Serie 2
### 2. April 2019
Tobias Weissert & Thomas Baumann

```
ip -6 addr
```
```
ping6 www.switch.ch ✓
```

![Router advertisment](./icmpv6Advertisment.png)

Source address in the router advertisment is the virtual network adapter of the VM host.

```
vtysh conf t interface ens4 
ipv6 address 2001:620:500:FF00::FF0D/64
ipv6 address FE80::FF0D/64

vtysh conf t interface ens3
ipv6 address 2001:620:500:FF0D::1/64
ipv6 address FE80::1/64
write mem
```

```
vtysh conf t interface ens4
ipv6 route ::/0 FE80::FC54:FF:FEE7:8557 250
write mem
```
```
ping6 switch.ch ✓
ping6 -i ens4 fe80::1 ✓
```
