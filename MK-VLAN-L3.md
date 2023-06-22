VLAN Layer 3
================================

![image](https://github.com/channith/Lab-Mikrotik/assets/63696723/c1e801f6-5b6f-48df-870d-58f8a0e50c1f)

### 1. Simple VLAN routing

- Vlan interface
```
/interface vlan
add comment=NE-MGMT interface=bonding1 name=VLAN2 vlan-id=2
````

- IP Address
```
/ip address
add address=10.22.22.15/24 interface=VLAN2 network=10.22.22.0
```

- Default route
```
/ip route
add distance=1 gateway=10.22.22.254
```

### 2. InterVLAN routing

![image](https://github.com/channith/Lab-Mikrotik/assets/63696723/254554c2-e82e-433a-b640-888b19aaa653)

To illustrate inter-VLAN communication, we will create a trunk that will carry traffic from three VLANs (VLAN2 and VLAN3, VLAN4)
across a single link between a Mikrotik router and a manageable switch that supports VLAN trunking.

Each VLAN has its own separate subnet (broadcast domain) as we see in figure above:

- VLAN 2 – 10.10.20.0/24;
- VLAN 3 – 10.10.30.0/24;
- VLAN 4 – 10.10.40.0./24.

Create VLAN interfaces:
```
  /interface vlan
add name=VLAN2 vlan-id=2 interface=ether1 disabled=no
add name=VLAN3 vlan-id=3 interface=ether1 disabled=no
add name=VLAN4 vlan-id=4 interface=ether1 disabled=no
```

Add IP addresses to VLANs:
```
/ip address
add address=10.10.20.1/24 interface=VLAN2
add address=10.10.30.1/24 interface=VLAN3
add address=10.10.40.1/24 interface=VLAN4
```
