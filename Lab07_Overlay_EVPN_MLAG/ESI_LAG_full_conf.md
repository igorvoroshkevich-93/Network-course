**Leaf-5**

```
hostname Leaf-5
!
link tracking group EVPN-ESI-MH
   recovery delay 60
!
vlan 905
   name Server-3
!
vrf instance Otus_Symmetric_L3
!
interface Port-Channel20
   switchport trunk allowed vlan 905
   switchport mode trunk
   !
   evpn ethernet-segment
      identifier 0000:0000:0905:0000:0056
      route-target import 00:00:09:05:00:56
      designated-forwarder election algoritm preference 120
   lacp system-id 0000.0905.0056
   link tracking group EVPN-ESI-MH downstream
!
interface Ethernet1
   link tracking group EVPN-ESI-MH upstream
!
interface Ethernet2
   link tracking group EVPN-ESI-MH upstream
!
interface Ethernet7
   channel-group 20 mode active
!
interface Ethernet8
!
interface Management1
!
interface Vlan905
   vrf Otus_Symmetric_L3
   ip address 172.16.2.2/24
   ip virtual-router address 172.16.2.1
!
ip virtual-router mac-address c0:01:09:05:00:56
!
ip routing
ip routing vrf Otus_Symmetric_L3
!
end

```

**Leaf-6**

```
hostname Leaf-6
!
link tracking group EVPN-ESI-MH
   recovery delay 60
!
vlan 905
   name Server-3
!
vrf instance Otus_Symmetric_L3
!
interface Port-Channel20
   switchport trunk allowed vlan 905
   switchport mode trunk
   !
   evpn ethernet-segment
      identifier 0000:0000:0905:0000:0056
      route-target import 00:00:09:05:00:56
      designated-forwarder election algorithm preference 110
   lacp system-id 0000.0905.0056
   link tracking group EVPN-ESI-MH downstream
!
interface Ethernet1
   link tracking group EVPN-ESI-MH upstream
!
interface Ethernet2
   link tracking group EVPN-ESI-MH upstream
!
interface Ethernet7
   channel-group 20 mode active
!
interface Ethernet8
!
interface Management1
!
interface Vlan905
   vrf Otus_Symmetric_L3
   ip address 172.16.2.3/24
   ip virtual-router address 172.16.2.1
!
ip virtual-router mac-address c0:01:09:05:00:56
!
ip routing
ip routing vrf Otus_Symmetric_L3
!
end

```


**Client-3**

```
!
hostname Server-3
!
vlan 905
   name Clients-3_to_Leaf5-6
!
interface Port-Channel10
   switchport trunk allowed vlan 905
   switchport mode trunk
!
interface Ethernet1
   channel-group 10 mode active
!
interface Ethernet2
   channel-group 10 mode active
!
interface Vlan905
   ip address 172.16.2.20/24
   no shutdown
!
ip routing
ip route 0.0.0.0 0.0.0.0 172.16.2.1
!
end

```
