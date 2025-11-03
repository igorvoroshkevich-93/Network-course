**DCI**

```
no aaa root
!
transceiver qsfp default-mode 4x10G
!
service routing protocols model multi-agent
!
hostname DCI-1
!
spanning-tree mode mstp
!
vlan 1000
   name GW-Vlan-1000-Client-1
!
vlan 1001
   name Vlan-1001-Client-2
!
vlan 1002
   name Vlan-1002-Client-3
!
vlan 1003
   name Vlan-1004-Client-4
!
interface Port-Channel20
   switchport trunk allowed vlan 1000-1003
   switchport mode trunk
!
interface Ethernet1
!
interface Ethernet2
!
interface Ethernet3
   shutdown
!
interface Ethernet4
   channel-group 20 mode active
!
interface Ethernet5
   switchport trunk allowed vlan 1000
   switchport mode trunk
   channel-group 20 mode active
!
interface Ethernet6
!
interface Ethernet7
!
interface Ethernet8
!
interface Management1
!
interface Vlan1000
   ip address 192.168.100.1/24
!
interface Vlan1001
   ip address 192.168.101.1/24
!
interface Vlan1002
   ip address 192.168.102.1/24
!
interface Vlan1003
   ip address 192.168.103.1/24
!
ip routing
!
end

```

