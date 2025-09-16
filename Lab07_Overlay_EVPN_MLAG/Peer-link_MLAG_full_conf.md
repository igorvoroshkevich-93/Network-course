
**Leaf-1**

```
service routing protocols model multi-agent
!
hostname Leaf-1
!
spanning-tree mode mstp
no spanning-tree vlan-id 4090
!
vlan 903
   name Server-1
!
vlan 4090
   name MLAG-peer-Leaf1-2
   trunk group MLAG-peer_Server-1
!
vrf instance Keepalive
!
vrf instance Otus_Symmetric_L3
!
interface Port-Channel10
   description MLAG_Leaf-2
   switchport mode trunk
   switchport trunk group MLAG-peer_Server-1
   spanning-tree link-type point-to-point
!
interface Port-Channel20
   switchport trunk allowed vlan 903
   switchport mode trunk
   mlag 10
!
interface Ethernet1
!
interface Ethernet2
!
interface Ethernet3
   no switchport
   vrf Keepalive
   ip address 10.3.0.0/31
!
interface Ethernet4
   description MLAG_Eth4_Leaf-2
   channel-group 10 mode active
!
interface Ethernet5
   description MLAG_Eth5_Leaf-2
   channel-group 10 mode active
!
interface Ethernet6
!
interface Ethernet7
   channel-group 20 mode active
!
interface Ethernet8
!
interface Management1
!
interface Vlan903
   vrf Otus_Symmetric_L3
   ip address 172.16.0.2/24
   ip virtual-router address 172.16.0.1
!
interface Vlan4090
   no autostate
   ip address 10.3.0.2/31
   ip ospf area 0.0.0.1
!
ip virtual-router mac-address c0:01:13:00:00:32
!
ip routing
ip routing vrf Keepalive
ip routing vrf Otus_Symmetric_L3
!
mlag configuration
   domain-id Leaf1-2
   local-interface Vlan4090
   peer-address 10.3.0.3
   peer-address heartbeat 10.3.0.1 vrf Keepalive
   peer-link Port-Channel10
   dual-primary detection delay 10 action errdisable all-interfaces
!
end

```

**Leaf-2**

```
hostname Leaf-2
!
no spanning-tree vlan-id 4090
!
vlan 903
   name Server-1
!
vlan 4090
   name MLAG-peer-Leaf2-1
   trunk group MLAG-peer_Server-1
!
vrf instance Keepalive
!
vrf instance Otus_Symmetric_L3
!
interface Port-Channel10
   description MLAG_Leaf-1
   switchport mode trunk
   switchport trunk group MLAG-peer_Server-1
   spanning-tree link-type point-to-point
!
interface Port-Channel20
   switchport trunk allowed vlan 903
   switchport mode trunk
   mlag 10
!
interface Ethernet1
!
interface Ethernet2
!
interface Ethernet3
   no switchport
   vrf Keepalive
   ip address 10.3.0.1/31
!
interface Ethernet4
   description MLAG_Eth4_Leaf-1
   channel-group 10 mode active
!
interface Ethernet5
   description MLAG_Eth5_Leaf-1
   channel-group 10 mode active
!
interface Ethernet6
!
interface Ethernet7
   channel-group 20 mode active
!
interface Ethernet8
!
interface Management1
!
interface Vlan903
   vrf Otus_Symmetric_L3
   ip address 172.16.0.3/24
   ip virtual-router address 172.16.0.1
!
interface Vlan4090
   no autostate
   ip address 10.3.0.3/31
   ip ospf area 0.0.0.1
!
ip virtual-router mac-address c0:01:13:00:00:32
!
ip routing
ip routing vrf Keepalive
ip routing vrf Otus_Symmetric_L3
!
mlag configuration
   domain-id Leaf1-2
   local-interface Vlan4090
   peer-address 10.3.0.2
   peer-address heartbeat 10.3.0.0 vrf Keepalive
   peer-link Port-Channel10
   dual-primary detection delay 10 action errdisable all-interfaces
!
end

```

**Leaf-3**

```
hostname Leaf-3
!
no spanning-tree vlan-id 4090
!
vlan 904
   name Server-2
!
vlan 4090
   name MLAG-peer-Leaf3-4
   trunk group MLAG-peer_Server-2
!
vrf instance Keepalive
!
vrf instance Otus_Symmetric_L3
!
interface Port-Channel10
   description MLAG_Leaf-4
   switchport mode trunk
   switchport trunk group MLAG-peer_Server-2
   spanning-tree link-type point-to-point
!
interface Port-Channel20
   switchport trunk allowed vlan 904
   switchport mode trunk
   mlag 10
!
interface Ethernet1
!
interface Ethernet2
!
interface Ethernet3
   no switchport
   vrf Keepalive
   ip address 10.3.1.0/31
!
interface Ethernet4
   description MLAG_Eth4_Leaf-4
   channel-group 10 mode active
!
interface Ethernet5
   description MLAG_Eth5_Leaf-4
   channel-group 10 mode active
!
interface Ethernet6
!
interface Ethernet7
   channel-group 20 mode active
!
interface Ethernet8
!
interface Management1
!
interface Vlan904
   vrf Otus_Symmetric_L3
   ip address 172.16.1.2/24
   ip virtual-router address 172.16.1.1
!
interface Vlan4090
   no autostate
   ip address 10.3.1.2/31
   ip ospf area 0.0.0.1
!
ip virtual-router mac-address c0:01:13:00:00:34
!
ip routing
ip routing vrf Keepalive
ip routing vrf Otus_Symmetric_L3
!
mlag configuration
   domain-id Leaf3-4
   local-interface Vlan4090
   peer-address 10.3.1.3
   peer-address heartbeat 10.3.1.1 vrf Keepalive
   peer-link Port-Channel10
   dual-primary detection delay 10 action errdisable all-interfaces
!
end

```


**Leaf-4**

```
hostname Leaf-4
!
no spanning-tree vlan-id 4090
!
vlan 904
   name Server-2
!
vlan 4090
   name MLAG-peer-Leaf4-3
   trunk group MLAG-peer_Server-2
!
vrf instance Keepalive
!
vrf instance Otus_Symmetric_L3
!
interface Port-Channel10
   description MLAG_Leaf-3
   switchport mode trunk
   switchport trunk group MLAG-peer_Server-2
   spanning-tree link-type point-to-point
!
interface Port-Channel20
   switchport trunk allowed vlan 904
   switchport mode trunk
   mlag 10
!
interface Ethernet1
!
interface Ethernet2
!
interface Ethernet3
   no switchport
   vrf Keepalive
   ip address 10.3.1.1/31
!
interface Ethernet4
   description MLAG_Eth4_Leaf-3
   channel-group 10 mode active
!
interface Ethernet5
   description MLAG_Eth5_Leaf-3
   channel-group 10 mode active
!
interface Ethernet6
!
interface Ethernet7
   channel-group 20 mode active
!
interface Ethernet8
!
interface Management1
!
interface Vlan904
   vrf Otus_Symmetric_L3
   ip address 172.16.1.3/24
   ip virtual-router address 172.16.1.1
!
interface Vlan4090
   no autostate
   ip address 10.3.1.3/31
   ip ospf area 0.0.0.1
!
ip virtual-router mac-address c0:01:13:00:00:34
!
ip routing
ip routing vrf Keepalive
ip routing vrf Otus_Symmetric_L3
!
mlag configuration
   domain-id Leaf3-4
   local-interface Vlan4090
   peer-address 10.3.1.2
   peer-address heartbeat 10.3.1.0 vrf Keepalive
   peer-link Port-Channel10
   dual-primary detection delay 10 action errdisable all-interfaces
!
end

```


**Client-1**

```
!
hostname Server-1
!
vlan 903
   name Clients-1_to_Leaf1-2
!
interface Port-Channel10
   switchport trunk allowed vlan 903
   switchport mode trunk
!
interface Ethernet1
   channel-group 10 mode active
!
interface Ethernet2
   channel-group 10 mode active
interface Vlan903
   ip address 172.16.0.10/24
!
ip routing
!
ip route 0.0.0.0 0.0.0.0 172.16.0.1
!

```

**Client-2**

```
!
hostname Server-2
!
vlan 904
   name Clients-2_to_Leaf3-4
!
interface Port-Channel10
   switchport trunk allowed vlan 904
   switchport mode trunk
!
interface Ethernet1
   channel-group 10 mode active
!
interface Ethernet2
   channel-group 10 mode active
interface Vlan904
   ip address 172.16.1.10/24
!
ip routing
!
ip route 0.0.0.0 0.0.0.0 172.16.1.1
!
```
