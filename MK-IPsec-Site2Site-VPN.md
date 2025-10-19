
<img width="928" height="275" alt="image" src="https://github.com/user-attachments/assets/b98f99af-4bca-4ca9-bee1-db1c4381741d" />


### MK-R01
- User

````
default user: admin
password:

Change your password
  
new password> mkr01
repeat new password> mkr01
````

- LACP
```
[admin@MikroTik] > interface/ethernet/comment ether2 comment=TO~SW01
[admin@MikroTik] > interface/bonding/add name=Po2 mode=802.3ad slaves=ether2
```

- VLAN
```
[admin@MikroTik] > interface/vlan/add vlan-id=10 interface=Po1 name=VLAN10
=> Error mtu, must reduce mtu as below:
[admin@MikroTik] > interface/vlan/add vlan-id=10 interface=Po1 name=VLAN10 mtu=1496
```

- Assign IP address on interface vlan
```
[admin@MikroTik] > ip/address/add interface=VLAN10 address=10.200.10.2/24
```

#### IPsec VPN

- IPsec Profile
First, create the IPsec Profile, where the IKE proposal is defined.

```
[admin@MikroTik] > ip/ipsec/profile/add name=VPN10 hash-algorithm=sha256 enc-algorithm=aes-
256 dh-group=ecp521 proposal-check=obey
```

- IPsec Proposal
In the next step, create a new IPsec Proposal for phase 2 encryption.
No Auth. Algorithms are needed since used AES-256-GCM as the encryption algorithm, which already includes the authentication.
```
[admin@MikroTik] > ip/ipsec/proposal/add name=VPN10 enc-algorithms=aes-256-gcm pfs-group=ec
p521
```

- IPsec Peer
For the peer configuration, set the name, IP address, IPsec profile, and Exchange Mode to IKEv2.
```
[admin@MikroTik] > ip/ipsec/peer/add name=VPN10 address=10.200.10.2 profile=VPN10 exchange-
mode=ike2 send-initial-contact=yes
```

- IPsec Identity
To set the authentication method using a pre-shared key, add a new IPsec Identity.
```
[admin@MikroTik] > ip/ipsec/identity/add peer=VPN10 secret=oursecret
```

- IPsec Policy
Next, define which networks will communicate with each other through the VPN tunnel.
```
[admin@MikroTik] > ip/ipsec/policy/add peer=VPN10 tunnel=yes proposal=VPN10 src-address=192
.168.10.0/24 dst-address=192.168.20.0/24 action=encrypt ipsec-protocols=esp 
```


- NAT
```
[admin@MikroTik] > ip/firewall/nat/add chain=srcnat src-address=192.168.10.0/24 dst-address
=192.168.20.0/24 action=accept 
```

- Route
> [admin@MikroTik] > ip/route/add dst-address=192.168.20.0/24 gateway=10.200.10.2

### MK-R02
- User

````
default user: admin
password:

Change your password
  
new password> mkr02
repeat new password> mkr02
````

- LACP
```
[admin@MikroTik] > interface/ethernet/comment ether1 comment=TO~SW01
[admin@MikroTik] > interface/bonding/add name=Po1 mode=802.3ad slaves=ether1 
```

- VLAN
```
[admin@MikroTik] > interface/vlan/add vlan-id=10 interface=Po1 name=VLAN10
=> Error mtu, must reduce mtu as below:
[admin@MikroTik] > interface/vlan/add vlan-id=10 interface=Po1 name=VLAN10 mtu=1496
```

- Assign IP address on interface vlan
```
[admin@MikroTik] > ip/address/add interface=VLAN10 address=10.200.10.2/24
```

#### IPsec VPN

- IPsec Profile
First, create the IPsec Profile, where the IKE proposal is defined.

```
[admin@MikroTik] > ip/ipsec/profile/add name=VPN10 hash-algorithm=sha256 enc-algorithm=aes-256 dh-group=ecp521 proposal-check=obey
```

- IPsec Proposal
In the next step, create a new IPsec Proposal for phase 2 encryption.
No Auth. Algorithms are needed since used AES-256-GCM as the encryption algorithm, which already includes the authentication.
```
[admin@MikroTik] > ip/ipsec/proposal/add name=VPN10 enc-algorithms=aes-256-gcm pfs-group=ecp521
```

- IPsec Peer
For the peer configuration, set the name, IP address, IPsec profile, and Exchange Mode to IKEv2.
```
[admin@MikroTik] > ip/ipsec/peer/add name=VPN10 address=10.200.10.1 profile=VPN10 exchange-mode=ike2 send-initial-contact=yes
```

- IPsec Identity
To set the authentication method using a pre-shared key, add a new IPsec Identity.
```
[admin@MikroTik] > ip/ipsec/identity/add peer=VPN10 secret=oursecret
```

- IPsec Policy
Next, define which networks will communicate with each other through the VPN tunnel.
```
[admin@MikroTik] > ip/ipsec/policy/add peer=VPN10 tunnel=yes proposal=VPN10 src-address=192.168.20.0/24 dst-address=192.168.10.0/24 action=encrypt ipsec-protocols=esp 
```

- NAT
```
[admin@MikroTik] > ip/firewall/nat/add chain=srcnat src-address=192.168.20.0/24 dst-address=192.168.10.0/24 action=accept 
```

- Route
> [admin@MikroTik] > ip/route/add dst-address=192.168.10.0/24 gateway=10.200.10.1


### SW01

- LACP
```
configure terminal
interface ethernet 0/1
description TO~MK-R02
channel-group 1 mode active
```

- Verity port-channel
```
SW01#show interfaces port-channel 1
Port-channel1 is up, line protocol is up (connected) 
  Hardware is EtherChannel, address is aabb.cc00.0110 (bia aabb.cc00.0110)
  MTU 1500 bytes, BW 10000 Kbit/sec, DLY 1000 usec, 
     reliability 255/255, txload 1/255, rxload 1/255
  Encapsulation ARPA, loopback not set
  Keepalive set (10 sec)
  Auto-duplex, Auto-speed, media type is unknown
  input flow-control is off, output flow-control is unsupported 
  Members in this channel: Et0/1 
  ARP type: ARPA, ARP Timeout 04:00:00
  Last input 00:00:23, output never, output hang never
  Last clearing of "show interface" counters never
  Input queue: 0/2000/0/0 (size/max/drops/flushes); Total output drops: 0
  Queueing strategy: fifo
  Output queue: 0/40 (size/max)
  5 minute input rate 0 bits/sec, 0 packets/sec
  5 minute output rate 0 bits/sec, 0 packets/sec
     55 packets input, 16902 bytes, 0 no buffer
     Received 17 broadcasts (0 multicasts)
     0 runts, 0 giants, 0 throttles 
     0 input errors, 0 CRC, 0 frame, 0 overrun, 0 ignored
     0 input packets with dribble condition detected
     114 packets output, 9768 bytes, 0 underruns
     0 output errors, 0 collisions, 0 interface resets
     0 unknown protocol drops
     0 babbles, 0 late collision, 0 deferred
     0 lost carrier, 0 no carrier
     0 output buffer failures, 0 output buffers swapped out
SW01#show etherchannel 1 summary 
Flags:  D - down        P - bundled in port-channel
        I - stand-alone s - suspended
        H - Hot-standby (LACP only)
        R - Layer3      S - Layer2
        U - in use      f - failed to allocate aggregator

        M - not in use, minimum links not met
        u - unsuitable for bundling
        w - waiting to be aggregated
        d - default port


Number of channel-groups in use: 1
Number of aggregators:           1

Group  Port-channel  Protocol    Ports
------+-------------+-----------+-----------------------------------------------
1      Po1(SU)         LACP      Et0/1(P)    

SW01#
SW01#show etherchannel port-channel 
Channel-group listing: 
----------------------

Group: 1 
----------
Port-channels in the group: 
---------------------------

Port-channel: Po1    (Primary Aggregator)

------------

Age of the Port-channel   = 0d:00h:29m:02s
Logical slot/port   = 16/0          Number of ports = 1
HotStandBy port = null 
Port state          = Port-channel Ag-Inuse 
Protocol            =   LACP
Port security       = Disabled

Ports in the Port-channel: 

Index   Load   Port     EC state        No of bits
------+------+------+------------------+-----------
  0     00     Et0/1    Active             0

Time since last port bundled:    0d:00h:12m:09s    Et0/1

SW01#
SW01#
SW01#
```

- VLAN
```
vlan 10
name P2P~MK-R01~MK-R02
#
```

- Propagae VLAN
```
int po1
switchport trunk encapsulation dot1q
switchport mode trunk
switchport trunk allowed vlan 10

```

### VPC1

> VPC4> ip 192.168.10.10/24 192.168.10.1

### VPC2

> VPC4> ip 192.168.20.10/24 192.168.20.1

```
VPC5> show ip

NAME        : VPC5[1]
IP/MASK     : 192.168.20.10/24
GATEWAY     : 192.168.20.1
DNS         : 
MAC         : 00:50:79:66:68:18
LPORT       : 20000
RHOST:PORT  : 127.0.0.1:30000
MTU         : 1500

VPC5> ping 192.168.20.1

84 bytes from 192.168.20.1 icmp_seq=1 ttl=64 time=0.862 ms
84 bytes from 192.168.20.1 icmp_seq=2 ttl=64 time=0.871 ms
84 bytes from 192.168.20.1 icmp_seq=3 ttl=64 time=0.849 ms
84 bytes from 192.168.20.1 icmp_seq=4 ttl=64 time=0.889 ms
84 bytes from 192.168.20.1 icmp_seq=5 ttl=64 time=0.900 ms

VPC5
```
