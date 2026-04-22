! Cisco 2911 Router – 4G-RTR (Primary Gateway)
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
ip route 192.168.20.0 255.255.255.0 192.168.10.2
!
line vty 0 4
 password cisco
 login
!
end
