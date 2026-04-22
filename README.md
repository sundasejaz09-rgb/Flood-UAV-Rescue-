## 1. Introduction

Modern surveillance operations increasingly rely on unmanned aerial vehicles (UAVs) for real‑time intelligence. A robust communication backbone is critical to ensure continuous command and control, telemetry reception, and data dissemination. This project models a **SkyGuard Defender** network that connects three surveillance drones and a field tablet to a central infrastructure via a wireless access router. The infrastructure includes core switching, a primary 4G router, a backup satellite link, and essential servers for MQTT messaging and web services.

### Objectives

- Design a hierarchical IP addressing scheme for infrastructure and drone subnets.
- Configure Cisco routers and switches for inter‑VLAN routing, DHCP, and static routing.
- Establish wireless connectivity for drones and mobile clients.
- Simulate ISP cloud links using serial connections.
- Validate end‑to‑end connectivity and failover paths.

---

## 2. Network Topology
The network consists of:

- **4G-RTR (Cisco 2911)** – Primary gateway to the ISP cloud.
- **SAT-RTR (Cisco 2911)** – Backup satellite gateway.
- **CORE-SW (Cisco 2960)** – Central switch connecting servers, rescue PC, and wireless router.
- **Router0 (HomeRouter-PT-AC)** – Wireless access point for the drone subnet.
- **MQTT-SRV** – MQTT broker for drone telemetry.
- **WEB-SRV** – Web interface for operator monitoring.
- **RESCUE-PC** – Wired operator workstation.
- **FIELD-TABLET** – Mobile operator device connected wirelessly.
- **DRONE-1, DRONE-2, DRONE-3** – Wireless end devices representing UAVs.
- **CLOUD-ISP** – Simulated ISP with serial links to both routers.

---

## 3. IP Addressing Scheme

| Subnet | Network Address | Devices |
|--------|-----------------|---------|
| Infrastructure (VLAN 1) | 192.168.10.0/24 | CORE-SW, Servers, RESCUE-PC, Router0 uplink |
| Drone Wireless | 192.168.20.0/24 | Router0, FIELD-TABLET, DRONE-1/2/3 |
| ISP Link 1 (4G) | 203.0.113.0/30 | 4G-RTR Serial0/0/0 ↔ Cloud Serial0 |
| ISP Link 2 (SAT) | 203.0.113.4/30 | SAT-RTR Serial0/0/0 ↔ Cloud Serial1 |
| Satellite Modem | 198.51.100.0/24 | SAT-RTR Gig0/0 (future expansion) |

**DHCP Pools:**
- Infrastructure clients (RESCUE-PC) obtain addresses from 4G-RTR (scope: 192.168.10.50–100).
- Drone subnet clients obtain addresses from Router0 (scope: 192.168.20.100–150).

---

## 4. Device Configurations

### 4.1 4G-RTR (Primary Gateway)

```cisco
hostname 4G-RTR
!
interface GigabitEthernet0/0
 description TO_CORE_SWITCH
 ip address 192.168.10.1 255.255.255.0
 no shutdown
!
interface Serial0/0/0
 description TO_CLOUD_ISP
 ip address 203.0.113.2 255.255.255.252
 clock rate 64000
 no shutdown
!
ip route 0.0.0.0 0.0.0.0 203.0.113.1
ip route 192.168.20.0 255.255.255.0 192.168.10.5
!
4.2 SAT-RTR (Backup Satellite)
cisco
hostname SAT-RTR
!
interface GigabitEthernet0/0
 description TO_SATELLITE_MODEM
 ip address 198.51.100.1 255.255.255.0
 no shutdown
!
interface Serial0/0/0
 description TO_CLOUD_ISP
 ip address 203.0.113.5 255.255.255.252
 no shutdown
!
ip route 0.0.0.0 0.0.0.0 203.0.113.4
!
4.3 CORE-SW
cisco
hostname CORE-SW
!
interface Vlan1
 ip address 192.168.10.2 255.255.255.0
 no shutdown
!
interface FastEthernet0/1
 switchport mode access
 switchport access vlan 1
 no shutdown
!
! (similar for other server ports)
ip default-gateway 192.168.10.1
4.4 Router0 (Wireless Drone Network)
SSID: SkyGuard-DroneNet

Security: WPA2‑Personal, Passphrase: DroneSecure2024

DHCP: Enabled, 192.168.20.100–150

Uplink: FastEthernet0 – IP 192.168.10.5/24

Default Gateway: 192.168.10.1

5. Wireless Client Configuration
Each drone and the field tablet are configured as wireless end devices:

SSID: SkyGuard-DroneNet

Authentication: WPA2‑PSK

Passphrase: DroneSecure2024

IP Assignment: DHCP

After association, devices receive IP addresses in the 192.168.20.0/24 subnet.

7. Redundancy & Failover
The network employs two independent ISP links:

Primary path: 4G-RTR → Cloud ISP (metric 1)

Backup path: SAT-RTR → Cloud ISP (floating static route with higher metric)

Floating static route on 4G-RTR (for Internet):

cisco
ip route 0.0.0.0 0.0.0.0 203.0.113.1
ip route 0.0.0.0 0.0.0.0 203.0.113.5 10
If the primary serial link fails, traffic automatically routes through SAT-RTR.
