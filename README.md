! Cisco 2960 Switch – CORE-SW
hostname CORE-SW
!
interface Vlan1
 ip address 192.168.10.2 255.255.255.0
 no shutdown
!
interface FastEthernet0/1
 description TO_4G-RTR_G0/0
 switchport mode access
 switchport access vlan 1
 no shutdown
!
interface FastEthernet0/2
 description TO_MQTT_SRV
 switchport mode access
 switchport access vlan 1
 no shutdown
!
interface FastEthernet0/3
 description TO_WEB_SRV
 switchport mode access
 switchport access vlan 1
 no shutdown
!
interface FastEthernet0/4
 description TO_RESCUE_PC
 switchport mode access
 switchport access vlan 1
 no shutdown
!
interface FastEthernet0/5
 description TO_Router0_UPLINK
 switchport mode access
 switchport access vlan 1
 no shutdown
!
ip default-gateway 192.168.10.1
!
end
