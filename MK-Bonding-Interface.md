Bonding Interface (LACP)
========================================================


### 1. Diagram
![image](https://github.com/channith/Lab-Mikrotik/assets/63696723/c1e801f6-5b6f-48df-870d-58f8a0e50c1f)


### 1. CLI Mikrotik Router
```MK-R1
/interface ethernet
set [ find default-name=sfp1 ] comment=TO~SW-HW1~GE0/0/6~Via~BoxCore1
set [ find default-name=sfp2 ] comment=TO~SW-HW1~GE0/0/7~Via~BoxCore1
/interface bonding
add mode=802.3ad name=bonding1 slaves=sfp1,sfp2
[noc@MikroTik] > 
```

### 2. CLI Huawei
```
interface Eth-Trunk15
 description TO~MK-R1
 port link-type trunk
 undo port trunk allow-pass vlan 1
 port trunk allow-pass vlan 2
 mode lacp
#
interface GigabitEthernet0/0/6
 description TO~MK-R1~sfp1~ODF-MBT-06~Core38
 eth-trunk 15
#
interface GigabitEthernet0/0/7
 description TO~MK-R1~sfp2~ODF-MBT-06~Core38
 eth-trunk 15
#
```

### 3. Testing P2P

- On MK-R
```
/ip address
add address=10.25.0.1/30 interface=bonding1
```
