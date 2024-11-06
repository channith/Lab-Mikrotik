## Mikrotik VPN Site-To-Site

A Site-to-Site VPN creates a secure and encrypted connection between two or more entire networks, such as your company's network and its branches, 
instead of connecting individual devices. Imagine physically connecting the networks through a private tunnel, but this tunnel is virtual and uses 
the public internet as a transport medium.

**Advantages of using a Site-to-Site VPN**:
- Connect remote offices: Allows company branches to securely access central network resources as if they were in the same location.
- Extended private network: Functions as an extended private local area network, even if the offices are geographically distant.
- Enhanced security: All communication between networks is encrypted, protecting confidential data.
- Cost reduction: Can be a more cost-effective alternative to leased lines.


### 1. Diagram
![image](https://github.com/user-attachments/assets/21f52389-9ffd-45fc-8f93-2243bc94926b)

### 2. Configuration

#### 2.1. Site-A
We perform configure basic such as user, password, IP address, DNS etc.

Below are parameter for configure IPsec Site-to-Site VPN Tunnels:

- IPsec Profile:
```
Name: vpn-site2site-1
Hash Algorithms: sha256
Encryption Algorithm: aes-256
DH group: modp 1024
```
- IPsec Proposal:
```
Name: vpn-site2site-1
Auth.Algorithm: sha256
Encr.Algorithm: aes-256 cbc
PFS Group: modp 1024
```

- IPsec Peer
```
Name: vpn-site2site-1
Address: 167.179.239.58
Profile: vpn-site2site-1
Exchange Mode: main
Tick: Send INITIAL_CONTACT
```

- IPsec Identities
```
Peer: vpn-site2site-1
Auth.Method: pre shared key
Secret: Test123
Policy Template: default
```

- IPsec Policy
```
Src Address: 192.168.200.0/24
Dst Address: 192.168.210.0/24
```

- IP>Firewall>NAT
```
Chain: srcnat
Src Address: 192.168.200.1/24
Dst Address: 192.168.210.1/24

Action: acept
```

#### 2.2. Site-B
