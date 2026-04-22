! Cisco 2911 Router – SAT-RTR (Satellite Backup)
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
end
