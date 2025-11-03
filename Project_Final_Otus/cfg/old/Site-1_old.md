**Agg-1**

```
no aaa root
!
transceiver qsfp default-mode 4x10G
!
service routing protocols model multi-agent
!
hostname Spine-1-Site-1
!
spanning-tree mode mstp
no spanning-tree vlan-id 4090
!
vlan 1000
   name To-Client-1_TOR-1
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
vlan 4090
   name MLAG-peer-Spine-2-Site-1
   trunk group MLAG-peer_Spine-2-Site-1
!
vrf instance Keepalive
!
interface Port-Channel10
   description MLAG_Spine-2-Site-1
   switchport mode trunk
   switchport trunk group MLAG-peer_Spine-2-Site-1
   spanning-tree link-type point-to-point
!
interface Port-Channel20
   switchport trunk allowed vlan 1000
   switchport mode trunk
   mlag 10
!
interface Port-Channel21
   switchport trunk allowed vlan 1000-1003
   switchport mode trunk
   mlag 11
!
interface Ethernet1
!
interface Ethernet2
!
interface Ethernet3
!
interface Ethernet4
!
interface Ethernet5
   channel-group 20 mode active
!
interface Ethernet6
!
interface Ethernet7
   no switchport
   vrf Keepalive
   ip address 10.30.0.0/31
!
interface Ethernet8
   description MLAG_Eth8_Spine-2-Site-1
   channel-group 10 mode active
!
interface Ethernet9
   description MLAG_Eth9_Spine-2-Site-1
   channel-group 10 mode active
!
interface Ethernet10
   switchport trunk allowed vlan 1000-1003
   switchport mode trunk
!
interface Ethernet11
   channel-group 21 mode active
!
interface Ethernet12
!
interface Ethernet13
!
interface Ethernet14
!
interface Ethernet15
!
interface Management1
!
interface Vlan4090
   no autostate
   ip address 10.30.0.2/31
!
ip routing
ip routing vrf Keepalive
!
mlag configuration
   domain-id Spine-Site-1
   local-interface Vlan4090
   peer-address 10.30.0.3
   peer-address heartbeat 10.30.0.1 vrf Keepalive
   peer-link Port-Channel10
   dual-primary detection delay 10 action errdisable all-interfaces
!
end

```

**Agg-2**

```
no aaa root
!
transceiver qsfp default-mode 4x10G
!
service routing protocols model multi-agent
!
hostname Spine-2-Site-1
!
spanning-tree mode mstp
no spanning-tree vlan-id 4090
!
vlan 1000
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
vlan 4090
   name MLAG-peer-Leaf1-2
   trunk group MLAG-peer_Spine-1-Site-1
!
vrf instance Keepalive
!
interface Port-Channel10
   description MLAG_Spine-1-Site-1
   switchport mode trunk
   switchport trunk group MLAG-peer_Spine-1-Site-1
   spanning-tree link-type point-to-point
!
interface Port-Channel20
   switchport trunk allowed vlan 1000
   switchport mode trunk
   mlag 10
!
interface Port-Channel21
   switchport trunk allowed vlan 1000-1003
   switchport mode trunk
   mlag 11
!
interface Ethernet1
!
interface Ethernet2
!
interface Ethernet3
!
interface Ethernet4
!
interface Ethernet5
   channel-group 20 mode active
!
interface Ethernet6
!
interface Ethernet7
   no switchport
   vrf Keepalive
   ip address 10.30.0.1/31
!
interface Ethernet8
   description MLAG_Eth8_Spine-1-Site-1
   channel-group 10 mode active
!
interface Ethernet9
   description MLAG_Eth9_Spine-1-Site-1
   channel-group 10 mode active
!
interface Ethernet10
   switchport trunk allowed vlan 1000-1003
   switchport mode trunk
!
interface Ethernet11
   channel-group 21 mode active
!
interface Ethernet12
!
interface Ethernet13
!
interface Ethernet14
!
interface Ethernet15
!
interface Management1
!
interface Vlan1001
   ip address 192.168.101.4/24
!
interface Vlan4090
   no autostate
   ip address 10.30.0.3/31
!
ip routing
ip routing vrf Keepalive
!
mlag configuration
   domain-id Spine-Site-1
   local-interface Vlan4090
   peer-address 10.30.0.2
   peer-address heartbeat 10.30.0.0 vrf Keepalive
   peer-link Port-Channel10
   dual-primary detection delay 10 action errdisable all-interfaces
!
end


```

**TOR-2**

```
no aaa root
!
transceiver qsfp default-mode 4x10G
!
service routing protocols model multi-agent
!
hostname Leaf-2-Site-1
!
spanning-tree mode mstp
!
vlan 1000
   name Vlan-1000-Client-1
!
interface Port-Channel20
   switchport trunk allowed vlan 1000
   switchport mode trunk
!
interface Ethernet1
!
interface Ethernet2
   channel-group 20 mode active
!
interface Ethernet3
!
interface Ethernet4
   channel-group 20 mode active
!
interface Ethernet5
!
interface Ethernet6
!
interface Ethernet7
!
interface Ethernet8
!
interface Ethernet9
   switchport trunk allowed vlan 1000
   switchport mode trunk
!
interface Ethernet10
!
interface Management1
!
ip routing
!
end

```


**Client-1**

```
no aaa root
!
transceiver qsfp default-mode 4x10G
!
service routing protocols model multi-agent
!
hostname Client-1
!
spanning-tree mode mstp
!
vlan 1000
   name OLD_CL_1
!
interface Ethernet1
   shutdown
!
interface Ethernet2
   shutdown
!
interface Ethernet3
   switchport trunk allowed vlan 1000
   switchport mode trunk
!
interface Ethernet4
!
interface Ethernet5
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
   ip address 192.168.100.2/24
!
ip routing
!
ip route 0.0.0.0/0 192.168.100.1
!
end
```

