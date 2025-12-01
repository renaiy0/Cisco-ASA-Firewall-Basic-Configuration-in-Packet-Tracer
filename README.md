# 🔥 Cisco ASA Firewall Basic Configuration in Packet Tracer

<div align="center">

![Cisco](https://img.shields.io/badge/Cisco-1BA0D7?style=for-the-badge&logo=cisco&logoColor=white)
![ASA](https://img.shields.io/badge/ASA_5505-FF0000?style=for-the-badge)
![Security](https://img.shields.io/badge/Security-00BCB4?style=for-the-badge)
![Packet Tracer](https://img.shields.io/badge/Packet_Tracer-00979D?style=for-the-badge)

**Multi-zone network security implementation with Cisco ASA Firewall**

*Protecting internal networks through advanced security policies and zone-based architecture*

[View Topology](#-network-topology) • [Configuration Guide](#-asa-configuration) • [Security Zones](#-security-zones)

</div>

---

## 📋 Project Overview

This project demonstrates a comprehensive Cisco ASA (Adaptive Security Appliance) 5505 firewall configuration implementing a multi-zone network architecture. The setup includes three distinct security zones (IN, OUT, DMZ) with different security levels, protecting internal resources while allowing controlled access to DMZ services and external networks.

**Key Implementation:**
- Three security zones with hierarchical security levels
- Zone-based firewall policies
- NAT/PAT configuration for internet access
- DMZ segmentation for public-facing services
- Security level enforcement (IN: 10, OUT: 0, DMZ: 100)

---

## 🖼️ Network Topology

<div align="center">
  <img src="assets/topology1.png" alt="ASA Firewall Network Topology" width="800"/>
  <p><em>Three-zone network architecture with ASA firewall at the center</em></p>
</div>

### Topology Components

The network consists of three distinct security zones:

**🟢 Internal Zone (IN)** - Security Level 10
- Network: 192.168.10.0/24
- 4 PC workstations (PC-PT PC0-PC3)
- 1 Layer 2 Switch connecting internal devices
- Router with FastEthernet interfaces
- Protected internal network resources

**🔴 Outside Zone (OUT)** - Security Level 0
- Network: 172.16.1.0/24
- External/Internet simulation
- 4 Server systems (Server-PT Server1-3, OUT server)
- Layer 2 Switch for external connectivity
- Untrusted network zone

**🔵 DMZ Zone (DMZ)** - Security Level 100
- Network: 10.10.10.0/24
- 3 Public-facing servers (Server-PT Server1-3)
- DMZ server at 10.10.10.10
- Layer 2 Switch for DMZ segmentation
- Semi-trusted zone for public services

**🌐 Central Security Device**
- Cisco ASA0 (5505-X model)
- Three GigabitEthernet interfaces (Gig1/1, Gig1/2, Gig1/3)
- Acts as security gateway between all zones

---

## 💻 ASA Configuration Screenshots

<div align="center">
  <img src="assets/cli1.png" alt="ASA Initial Configuration" width="800"/>
  <p><em>ASA firewall initial setup and interface configuration</em></p>
</div>

<div align="center">
  <img src="assets/cli2.png" alt="ASA Security Configuration" width="800"/>
  <p><em>Security levels, NAT, and policy configuration</em></p>
</div>

---

## 🔧 Security Zone Architecture

```
                    INTERNET (Untrusted)
                            │
                    ┌───────▼────────┐
                    │   OUT Zone     │
                    │ Security: 0    │
                    │ 172.16.1.0/24  │
                    └───────┬────────┘
                            │
                    ┌───────▼────────┐
                    │  Cisco ASA0    │
                    │   (5505-X)     │
                    │   Firewall     │
                    └─┬─────┬────┬───┘
                      │     │    │
        ┌─────────────┘     │    └──────────────┐
        │                   │                   │
┌───────▼────────┐  ┌──────▼────────┐  ┌───────▼────────┐
│   IN Zone      │  │   DMZ Zone    │  │  Additional    │
│ Security: 10   │  │ Security: 100 │  │  Networks      │
│192.168.10.0/24 │  │ 10.10.10.0/24 │  │                │
└────────────────┘  └───────────────┘  └────────────────┘
   Internal LAN      Public Services    Future Expansion
```

### Security Level Hierarchy

| Zone | Security Level | Network | Purpose |
|------|---------------|---------|---------|
| **IN** | 10 | 192.168.10.0/24 | Internal trusted network |
| **OUT** | 0 | 172.16.1.0/24 | External untrusted network |
| **DMZ** | 100 | 10.10.10.0/24 | Public-facing services |

**Security Rules:**
- Higher security level → Lower security level: Allowed by default
- Lower security level → Higher security level: Denied by default (requires ACL)
- Same security level → Same security level: Denied by default

---

## 🛠️ Complete ASA Configuration

### Step 1: Initial Setup and Hostname

```cisco
ciscoasa> enable
ciscoasa# configure terminal
ciscoasa(config)# hostname PARIMETER
PARIMETER(config)# 
```

### Step 2: Enable Password Configuration

```cisco
PARIMETER(config)# enable password maliq
PARIMETER(config)# 
```

### Step 3: Configure Inside Interface (IN Zone)

```cisco
PARIMETER(config)# interface gigabitEthernet 1/1
PARIMETER(config-if)# nameif IN
INFO: Security level for "IN" set to 0 by default.
PARIMETER(config-if)# security-level 10
PARIMETER(config-if)# ip address 192.168.10.1 255.255.255.0
PARIMETER(config-if)# no shutdown
PARIMETER(config-if)# exit
```

### Step 4: Configure Outside Interface (OUT Zone)

```cisco
PARIMETER(config)# interface gigabitEthernet 1/2
PARIMETER(config-if)# nameif OUT
INFO: Security level for "OUT" set to 0 by default.
PARIMETER(config-if)# security-level 0
PARIMETER(config-if)# ip address 172.16.1.1 255.255.0.0
PARIMETER(config-if)# no shutdown
PARIMETER(config-if)# exit
```

### Step 5: Configure DMZ Interface

```cisco
PARIMETER(config)# interface gigabitEthernet 1/3
PARIMETER(config-if)# nameif DMZ
INFO: Security level for "DMZ" set to 0 by default.
PARIMETER(config-if)# security-level 100
PARIMETER(config-if)# ip address 10.10.10.1 255.0.0.0
PARIMETER(config-if)# no shutdown
PARIMETER(config-if)# exit
```

### Step 6: Configure NAT for Internet Access

```cisco
# Configure PAT (Port Address Translation) for internal network
PARIMETER(config)# object network IN-NET
PARIMETER(config-network-object)# subnet 192.168.10.0 255.255.255.0
PARIMETER(config-network-object)# nat (IN,OUT) dynamic interface
PARIMETER(config-network-object)# exit
```

### Step 7: Configure Static Route (if needed)

```cisco
PARIMETER(config)# route OUT 0.0.0.0 0.0.0.0 172.16.1.254
```

### Step 8: Configure Access Control (Optional)

```cisco
# Allow DMZ to be accessed from outside (example for web server)
PARIMETER(config)# access-list OUT_IN extended permit tcp any host 10.10.10.10 eq 80
PARIMETER(config)# access-list OUT_IN extended permit tcp any host 10.10.10.10 eq 443
PARIMETER(config)# access-group OUT_IN in interface OUT
```

### Step 9: Configure Password Encryption

```cisco
PARIMETER(config)# username MAL password maliq
PARIMETER(config)# enable password maliq encrypted
```

### Step 10: Save Configuration

```cisco
PARIMETER(config)# exit
PARIMETER# write memory
Building configuration...
Cryptochecksum: 4a8d69cc 5cab1eeb 21822fe9 2ba2475f

[OK]
```

---

## 🔍 Verification Commands

### Check Interface Status

```cisco
PARIMETER# show interface ip brief
Interface                  IP-Address      OK? Method Status                Protocol
GigabitEthernet1/1         192.168.10.1    YES manual up                    up
GigabitEthernet1/2         172.16.1.1      YES manual up                    up
GigabitEthernet1/3         10.10.10.1      YES manual up                    up
```

### Verify Security Levels

```cisco
PARIMETER# show nameif
Interface                Name                     Security
GigabitEthernet1/1       IN                       10
GigabitEthernet1/2       OUT                      0
GigabitEthernet1/3       DMZ                      100
```

### Check NAT Configuration

```cisco
PARIMETER# show nat
NAT policies on Interface IN:
  match ip IN-NET 192.168.10.0 255.255.255.0 OUT any
    dynamic translation to interface
```

### View Running Configuration

```cisco
PARIMETER# show running-config
```

### Test Connectivity

```cisco
# From internal PC to DMZ server
PC> ping 10.10.10.10

# From internal PC to outside
PC> ping 172.16.1.10

# From DMZ to internal (should fail without explicit ACL)
Server-DMZ> ping 192.168.10.10
```

---

## 📊 Network Specifications

| Parameter | Value |
|-----------|-------|
| **Internal Network (IN)** | 192.168.10.0/24 |
| **External Network (OUT)** | 172.16.1.0/24 |
| **DMZ Network** | 10.10.10.0/24 |
| **ASA Model** | 5505-X |
| **ASA Version** | 9.6(1) |
| **Hostname** | PARIMETER |
| **Management Access** | Console, SSH (if configured) |

---

## 🎯 Key Features Implemented

✅ **Multi-Zone Segmentation** - Three distinct security zones with different trust levels  
🔒 **Security Level Enforcement** - Hierarchical security policy based on zone trust  
🌐 **NAT/PAT Configuration** - Internal network can access internet via PAT  
🛡️ **Stateful Inspection** - ASA tracks connection state for security  
📡 **DMZ Isolation** - Public servers isolated from internal network  
⚡ **Interface-Based Policies** - Security rules applied per interface  
🔐 **Password Protection** - Encrypted passwords for device access  
📋 **Access Control Lists** - Optional granular traffic control

---

## 🔒 Security Best Practices Implemented

### 1. Zone-Based Security
- Clear separation between trusted, untrusted, and semi-trusted zones
- Security levels enforce default traffic policies
- DMZ provides buffer between internal and external networks

### 2. Default Deny Policy
- Traffic from lower to higher security levels denied by default
- Explicit ACLs required for external access to internal resources
- Principle of least privilege

### 3. Password Security
```cisco
# Strong password policy
PARIMETER(config)# enable password maliq encrypted
PARIMETER(config)# username MAL password maliq
```

### 4. NAT Security
- Hide internal IP addressing from external networks
- PAT conserves public IP addresses
- Adds layer of obscurity for internal topology

---

## 🚨 Traffic Flow Rules

### Allowed by Default (Higher → Lower Security)

```
IN (10) → OUT (0)     ✅ Allowed
IN (10) → DMZ (100)   ❌ Denied (same as lower → higher)
DMZ (100) → OUT (0)   ✅ Allowed
```

### Denied by Default (Lower → Higher Security)

```
OUT (0) → IN (10)     ❌ Denied (requires ACL)
OUT (0) → DMZ (100)   ❌ Denied (requires ACL)
DMZ (100) → IN (10)   ❌ Denied (requires ACL)
```

### Example ACL to Allow Specific Traffic

```cisco
# Allow external access to DMZ web server
PARIMETER(config)# access-list OUT_DMZ extended permit tcp any host 10.10.10.10 eq 80
PARIMETER(config)# access-list OUT_DMZ extended permit tcp any host 10.10.10.10 eq 443
PARIMETER(config)# access-group OUT_DMZ in interface OUT

# Allow DMZ to access specific internal resource
PARIMETER(config)# access-list DMZ_IN extended permit tcp host 10.10.10.10 host 192.168.10.100 eq 3306
PARIMETER(config)# access-group DMZ_IN in interface DMZ
```

---

## 🧪 Testing Scenarios

### Scenario 1: Internal to Internet Access
```cisco
# From PC in IN zone
PC0> ping 172.16.1.10
Result: Should succeed (IN → OUT allowed)
```

### Scenario 2: Internal to DMZ Access
```cisco
# From PC in IN zone
PC0> ping 10.10.10.10
Result: Should fail (need ACL to allow)
```

### Scenario 3: External to DMZ Access
```cisco
# From Server in OUT zone
Server1> ping 10.10.10.10
Result: Should fail (need ACL with static NAT)
```

### Scenario 4: DMZ to Internet Access
```cisco
# From Server in DMZ zone
DMZ-Server> ping 172.16.1.10
Result: Should succeed (DMZ → OUT allowed)
```

---

## 📁 Project Files

```
cisco-asa-basic-config/
├── assets/
│   ├── topology1.png         # Network topology diagram
│   ├── cli1.png             # ASA configuration screenshot 1
│   └── cli2.png             # ASA configuration screenshot 2
├── configs/
│   ├── asa-startup.cfg      # Initial ASA configuration
│   └── asa-final.cfg        # Complete configuration
├── packet-tracer/
│   └── asa-firewall.pkt     # Packet Tracer file
└── README.md
```

---

## 🎓 Learning Objectives

After completing this project, you will understand:

- ✅ Cisco ASA firewall basic configuration
- ✅ Security zone implementation and design
- ✅ Security level concept and traffic flow
- ✅ NAT/PAT configuration on ASA
- ✅ Interface naming and IP addressing
- ✅ Access control list (ACL) configuration
- ✅ Multi-zone network architecture
- ✅ DMZ implementation and security
- ✅ Default security policies
- ✅ Stateful firewall operation

---

## 🚀 Advanced Configuration Ideas

<details>
<summary><b>📝 Site-to-Site VPN</b></summary>

Configure IPsec VPN tunnel between two ASA firewalls for secure remote site connectivity.
</details>

<details>
<summary><b>🔐 Remote Access VPN</b></summary>

Implement SSL VPN or IPsec VPN for remote user access to internal resources.
</details>

<details>
<summary><b>🌐 Multiple DMZ Zones</b></summary>

Create separate DMZ zones for web servers, mail servers, and other public services.
</details>

<details>
<summary><b>📊 Traffic Inspection</b></summary>

Enable deep packet inspection and application layer filtering.
</details>

<details>
<summary><b>🔍 Logging and Monitoring</b></summary>

Configure syslog server and SNMP for centralized logging and monitoring.
```cisco
PARIMETER(config)# logging enable
PARIMETER(config)# logging host IN 192.168.10.100
PARIMETER(config)# logging trap informational
```
</details>

---

## 🐛 Troubleshooting

### Issue 1: Cannot Access Internet from Internal Network

**Symptoms:** PCs in IN zone cannot ping external hosts

**Solution:**
```cisco
# Check NAT configuration
PARIMETER# show nat

# Verify default route
PARIMETER# show route

# Ensure PAT is configured
PARIMETER(config)# object network IN-NET
PARIMETER(config-network-object)# nat (IN,OUT) dynamic interface
```

### Issue 2: DMZ Cannot Communicate

**Symptoms:** DMZ servers cannot reach any network

**Solution:**
```cisco
# Check interface status
PARIMETER# show interface gigabitEthernet 1/3

# Verify security level
PARIMETER# show nameif

# Check routing
PARIMETER# show route dmz
```

### Issue 3: External Access to DMZ Fails

**Symptoms:** Cannot access DMZ services from internet

**Solution:**
```cisco
# Create static NAT for DMZ server
PARIMETER(config)# object network DMZ-WEB
PARIMETER(config-network-object)# host 10.10.10.10
PARIMETER(config-network-object)# nat (DMZ,OUT) static 172.16.1.100

# Add ACL to allow access
PARIMETER(config)# access-list OUT_IN permit tcp any host 172.16.1.100 eq 80
PARIMETER(config)# access-group OUT_IN in interface OUT
```

---

## 📚 Additional Resources

- [Cisco ASA Configuration Guide](https://www.cisco.com/c/en/us/support/security/asa-5500-series-next-generation-firewalls/products-installation-and-configuration-guides-list.html)
- [Cisco ASA Command Reference](https://www.cisco.com/c/en/us/td/docs/security/asa/asa-command-reference/A-H/cmdref1.html)
- [ASA Security Levels Explained](https://www.cisco.com/c/en/us/support/docs/security/asa-5500-x-series-next-generation-firewalls/113327-asa-interface-00.html)
- [NAT on ASA Configuration](https://www.cisco.com/c/en/us/support/docs/security/asa-5500-x-series-next-generation-firewalls/100201-asa-nat-config.html)

---

## 🤝 Contributing

Contributions welcome! To contribute:

1. Fork the repository
2. Create feature branch (`git checkout -b feature/Enhancement`)
3. Commit changes (`git commit -m 'Add security enhancement'`)
4. Push to branch (`git push origin feature/Enhancement`)
5. Open Pull Request

---

## 📄 License

This project is licensed under the MIT License - free to use for educational purposes.

---


<div align="center">

**Building Secure Networks with Cisco ASA** 🔥

*One security zone at a time*

**[⬆ Back to Top](#-cisco-asa-firewall-basic-configuration-in-packet-tracer)**

</div>
