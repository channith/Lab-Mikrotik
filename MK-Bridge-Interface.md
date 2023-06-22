Bridge Interface
=========================================================


- Create bridge interface
```
/interface bridge
add name=Bridge_LAN
```

- Bind port to bridge
```
/interface bridge port
add bridge=Bridge_LAN interface=ether2
add bridge=Bridge_LAN interface=ether3
add bridge=Bridge_LAN interface=ether4
add bridge=Bridge_LAN interface=ether5
add bridge=Bridge_LAN interface=ether6
```

- Assign IP Address
```
/ip address
add address=10.60.3.222/30 interface=ether1 network=10.60.3.220 ##ether1 is uplink which connect to ISP switch/router
add address=45.115.211.105/29 interface=Bridge_LAN network=45.115.211.104
```

- Route
```
/ip route
add distance=1 gateway=10.60.3.221
```
