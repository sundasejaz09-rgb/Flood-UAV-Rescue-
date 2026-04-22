! HomeRouter-PT-AC – Router0 (Drone Network)
hostname Router0
!
interface Vlan1
 ip address 192.168.20.1 255.255.255.0
 no shutdown
!
interface FastEthernet0
 description UPLINK_TO_CORE_SW
 ip address 192.168.10.5 255.255.255.0
 no shutdown
!
ip route 0.0.0.0 0.0.0.0 192.168.10.1
!
! Wireless settings (configured via GUI)
! SSID: SkyGuard-DroneNet
! Security: WPA2-Personal, Passphrase: DroneSecure2024
! DHCP pool: 192.168.20.100 - 192.168.20.150
!
end
