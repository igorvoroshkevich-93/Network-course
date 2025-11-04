**Spine1-1**

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
   no switchport
   ip address 10.2.0.0/31
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.1
   ip ospf message-digest-key 10 md5 7 f3D/O8/wE3IGG+uuvYhESw==
!
interface Ethernet2
   no switchport
   ip address 10.2.0.2/31
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.1
   ip ospf message-digest-key 10 md5 7 DYWREOc3d9i1hc3SA03m/g==
!
interface Ethernet3
   no switchport
   ip address 10.2.0.4/31
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.1
   ip ospf message-digest-key 10 md5 7 DYWREOc3d9i1hc3SA03m/g==
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
interface Loopback0
   ip address 10.0.0.0/32
   ip ospf area 0.0.0.1
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
ip prefix-list Leaf_LB seq 10 permit 10.0.0.32/32
ip prefix-list Leaf_LB seq 20 permit 10.0.0.33/32
ip prefix-list Leaf_LB seq 30 permit 10.0.0.34/32
ip prefix-list Leaf_LB seq 70 permit 10.1.0.32/32
ip prefix-list Leaf_LB seq 80 permit 10.1.0.34/32
ip prefix-list Spine_LB seq 10 permit 10.0.0.0/32
ip prefix-list Spine_LB seq 20 permit 10.1.0.0/32
!
mlag configuration
   domain-id Spine-Site-1
   local-interface Vlan4090
   peer-address 10.30.0.3
   peer-address heartbeat 10.30.0.1 vrf Keepalive
   peer-link Port-Channel10
   dual-primary detection delay 10 action errdisable all-interfaces
!
route-map Loopback permit 10
   match ip address prefix-list Spine_LB
!
route-map Loopback permit 20
   match ip address prefix-list Leaf_LB
!
peer-filter ASN_Leafs
   10 match as-range 65100-65199 result accept
!
router bgp 65100
   router-id 10.0.0.0
   maximum-paths 4 ecmp 64
   bgp listen range 10.0.0.0/24 peer-group EVPN_Overlay peer-filter ASN_Leafs
   neighbor EVPN_Overlay peer group
   neighbor EVPN_Overlay remote-as 65100
   neighbor EVPN_Overlay update-source Loopback0
   neighbor EVPN_Overlay ebgp-multihop 10
   neighbor EVPN_Overlay route-reflector-client
   neighbor EVPN_Overlay timers 30 90
   neighbor EVPN_Overlay password 7 9aEh1CE6iI7OAWCvGTRLsw==
   neighbor EVPN_Overlay send-community extended
   redistribute connected route-map Loopback
   !
   address-family evpn
      neighbor EVPN_Overlay activate
!
router ospf 1
   router-id 10.0.0.0
   passive-interface default
   no passive-interface Ethernet1
   no passive-interface Ethernet2
   no passive-interface Ethernet3
   max-lsa 12000
!
end
```

**Spine1-2**

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
   no switchport
   ip address 10.2.1.0/31
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.1
   ip ospf message-digest-key 10 md5 7 f3D/O8/wE3IGG+uuvYhESw==
!
interface Ethernet2
   no switchport
   ip address 10.2.1.2/31
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.1
   ip ospf message-digest-key 10 md5 7 DYWREOc3d9i1hc3SA03m/g==
!
interface Ethernet3
   no switchport
   ip address 10.2.1.4/31
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.1
   ip ospf message-digest-key 10 md5 7 DYWREOc3d9i1hc3SA03m/g==
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
interface Loopback0
   ip address 10.0.0.1/32
   ip ospf area 0.0.0.1
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
ip prefix-list Leaf_LB seq 10 permit 10.0.0.32/32
ip prefix-list Leaf_LB seq 20 permit 10.0.0.33/32
ip prefix-list Leaf_LB seq 30 permit 10.0.0.34/32
ip prefix-list Leaf_LB seq 70 permit 10.1.0.32/32
ip prefix-list Leaf_LB seq 80 permit 10.1.0.34/32
ip prefix-list Spine_LB seq 10 permit 10.0.0.0/32
ip prefix-list Spine_LB seq 20 permit 10.1.0.0/32
!
mlag configuration
   domain-id Spine-Site-1
   local-interface Vlan4090
   peer-address 10.30.0.2
   peer-address heartbeat 10.30.0.0 vrf Keepalive
   peer-link Port-Channel10
   dual-primary detection delay 10 action errdisable all-interfaces
!
route-map Loopback permit 10
   match ip address prefix-list Spine_LB
!
route-map Loopback permit 20
   match ip address prefix-list Leaf_LB
!
peer-filter ASN_Leafs
   10 match as-range 65100-65199 result accept
!
router bgp 65100
   router-id 10.0.0.1
   maximum-paths 4 ecmp 64
   bgp listen range 10.0.0.0/24 peer-group EVPN_Overlay peer-filter ASN_Leafs
   neighbor EVPN_Overlay peer group
   neighbor EVPN_Overlay remote-as 65100
   neighbor EVPN_Overlay update-source Loopback0
   neighbor EVPN_Overlay ebgp-multihop 10
   neighbor EVPN_Overlay route-reflector-client
   neighbor EVPN_Overlay timers 30 90
   neighbor EVPN_Overlay password 7 9aEh1CE6iI7OAWCvGTRLsw==
   neighbor EVPN_Overlay send-community extended
   redistribute connected route-map Loopback
   !
   address-family evpn
      neighbor EVPN_Overlay activate
!
router ospf 1
   router-id 10.0.0.1
   passive-interface default
   no passive-interface Ethernet1
   no passive-interface Ethernet2
   no passive-interface Ethernet3
   max-lsa 12000
!
end
```


**Leaf1-1**

```
no aaa root
!
transceiver qsfp default-mode 4x10G
!
service routing protocols model multi-agent
!
hostname Leaf-1-Site-1
!
spanning-tree mode mstp
!
vlan 1000
   name multisite-l3_1000
!
vlan 2000
   name multisite-l2_2000
!
vrf instance Multisite-1
!
interface Ethernet1
   no switchport
   ip address 10.2.0.1/31
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.1
   ip ospf message-digest-key 10 md5 7 f3D/O8/wE3IGG+uuvYhESw==
!
interface Ethernet2
!
interface Ethernet3
   no switchport
   ip address 10.2.1.1/31
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.1
   ip ospf message-digest-key 10 md5 7 DYWREOc3d9i1hc3SA03m/g==
!
interface Ethernet4
!
interface Ethernet5
   description DCI-1
   no switchport
   ip address 10.100.0.1/31
!
interface Ethernet6
!
interface Ethernet7
!
interface Ethernet8
!
interface Loopback0
   ip address 10.0.0.32/32
   ip ospf area 0.0.0.1
!
interface Loopback1
   ip address 10.1.0.32/32
   ip ospf area 0.0.0.1
!
interface Management1
!
interface Vlan1000
   description Multisite-1_1000
   vrf Multisite-1
   ip address virtual 192.168.100.1/24
!
interface Vxlan1
   vxlan source-interface Loopback1
   vxlan udp-port 4789
   vxlan vlan 1000 vni 11000
   vxlan vlan 2000 vni 12000
   vxlan vrf Multisite-1 vni 20001
   vxlan learn-restrict any
!
ip routing
ip routing vrf Multisite-1
!
ip prefix-list Leaf_LB seq 10 permit 10.0.0.32/32
ip prefix-list Leaf_LB seq 20 permit 10.1.0.32/32
!
route-map Loopback permit 10
   match ip address prefix-list Leaf_LB
!
router bgp 65100
   router-id 10.0.0.32
   maximum-paths 4 ecmp 64
   neighbor EVPN_Overlay peer group
   neighbor EVPN_Overlay remote-as 65100
   neighbor EVPN_Overlay update-source Loopback0
   neighbor EVPN_Overlay ebgp-multihop 10
   neighbor EVPN_Overlay timers 30 90
   neighbor EVPN_Overlay password 7 9aEh1CE6iI7OAWCvGTRLsw==
   neighbor EVPN_Overlay send-community extended
   neighbor evpn-dci-peers peer group
   neighbor evpn-dci-peers next-hop-unchanged
   neighbor evpn-dci-peers update-source Loopback0
   neighbor evpn-dci-peers allowas-in 1
   neighbor evpn-dci-peers ebgp-multihop 2
   neighbor evpn-dci-peers timers 1 3
   neighbor evpn-dci-peers send-community extended
   neighbor evpn-dci-peers maximum-routes 0 warning-limit 90 percent
   neighbor underlay-export-local peer group
   neighbor underlay-export-local timers 1 3
   neighbor underlay-export-local route-map all-loopbacks in
   neighbor underlay-export-local route-map local-loopbacks out
   neighbor underlay-export-local maximum-routes 0 warning-limit 90 percent
   neighbor 10.0.0.0 peer group EVPN_Overlay
   neighbor 10.0.0.1 peer group EVPN_Overlay
   neighbor 10.100.0.0 peer group underlay-export-local
   neighbor 10.100.0.0 remote-as 65000
   neighbor 10.100.0.0 description dci-r1
   neighbor 10.101.0.99 peer group evpn-dci-peers
   neighbor 10.101.0.99 remote-as 65000
   neighbor 10.101.0.99 description dci-r1
   redistribute connected route-map all-loopbacks
   !
   vlan 1000
      rd 10.1.0.32:11000
      route-target both 65100:11000
      redistribute learned
   !
   vlan 2000
      rd evpn domain all 10.1.0.32:12000
      route-target import export evpn domain all 12000:65000
      redistribute learned
   !
   address-family evpn
      neighbor EVPN_Overlay activate
      neighbor evpn-dci-peers activate
      neighbor evpn-dci-peers domain remote
      neighbor default next-hop-self received-evpn-routes route-type ip-prefix inter-domain
   !
   address-family ipv4
      neighbor underlay-export-local activate
   !
   vrf Multisite-1
      rd 10.1.0.32:20001
      route-target import evpn 20001:65000
      route-target export evpn 20001:65000
      redistribute connected
!
router ospf 1
   router-id 10.0.0.32
   passive-interface default
   no passive-interface Ethernet1
   no passive-interface Ethernet2
   no passive-interface Ethernet3
   no passive-interface Port-Channel10
   max-lsa 12000
!
end


```

**Leaf1-2**

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
no spanning-tree vlan-id 4091
!
vlan 1000
   name Vlan-1000-Client-1
!
vlan 2000
   name multisite-l2_2000
!
vlan 4091
   name MLAG-peer-Leaf1-2
   trunk group MLAG-peer_Server-1
!
vrf instance Keepalive
!
interface Port-Channel11
   description MLAG_Leaf-3
   switchport mode trunk
   switchport trunk group MLAG-peer_Server-1
   spanning-tree link-type point-to-point
!
interface Port-Channel20
   shutdown
   switchport trunk allowed vlan 1000
   switchport mode trunk
!
interface Port-Channel22
   switchport trunk allowed vlan 1000,2000
   switchport mode trunk
   mlag 10
!
interface Ethernet1
   no switchport
   ip address 10.2.0.3/31
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.1
   ip ospf message-digest-key 10 md5 7 f3D/O8/wE3IGG+uuvYhESw==
!
interface Ethernet2
   channel-group 20 mode active
!
interface Ethernet3
   no switchport
   ip address 10.2.1.3/31
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.1
   ip ospf message-digest-key 10 md5 7 DYWREOc3d9i1hc3SA03m/g==
!
interface Ethernet4
   channel-group 20 mode active
!
interface Ethernet5
   no switchport
   vrf Keepalive
   ip address 10.3.0.0/31
!
interface Ethernet6
   description MLAG_Eth6_Leaf-3
   channel-group 11 mode active
!
interface Ethernet7
   description MLAG_Eth7_Leaf-3
   channel-group 11 mode active
!
interface Ethernet8
   channel-group 22 mode active
!
interface Ethernet9
   switchport trunk allowed vlan 1000
   switchport mode trunk
!
interface Ethernet10
!
interface Loopback0
   ip address 10.0.0.33/32
   ip ospf area 0.0.0.1
!
interface Loopback1
   ip address 10.1.0.34/32
   ip ospf area 0.0.0.1
!
interface Management1
!
interface Vlan4091
   no autostate
   ip address 10.3.0.2/31
   ip ospf area 0.0.0.1
!
interface Vxlan1
   vxlan source-interface Loopback1
   vxlan udp-port 4789
   vxlan vlan 1000 vni 11000
   vxlan vlan 2000 vni 12000
   vxlan learn-restrict any
!
ip virtual-router mac-address c0:01:09:05:00:56
!
ip routing
ip routing vrf Keepalive
!
ip prefix-list Leaf_LB seq 10 permit 10.0.0.33/32
ip prefix-list Leaf_LB seq 20 permit 10.1.0.34/32
!
mlag configuration
   domain-id Leaf2-3-Site-1
   local-interface Vlan4091
   peer-address 10.3.0.3
   peer-address heartbeat 10.3.0.1 vrf Keepalive
   peer-link Port-Channel11
   dual-primary detection delay 10 action errdisable all-interfaces
!
route-map Loopback permit 10
   match ip address prefix-list Leaf_LB
!
router bgp 65100
   router-id 10.0.0.33
   maximum-paths 4 ecmp 64
   neighbor EVPN_Overlay peer group
   neighbor EVPN_Overlay remote-as 65100
   neighbor EVPN_Overlay update-source Loopback0
   neighbor EVPN_Overlay ebgp-multihop 10
   neighbor EVPN_Overlay timers 30 90
   neighbor EVPN_Overlay password 7 9aEh1CE6iI7OAWCvGTRLsw==
   neighbor EVPN_Overlay send-community extended
   neighbor 10.0.0.0 peer group EVPN_Overlay
   neighbor 10.0.0.1 peer group EVPN_Overlay
   redistribute connected route-map Loopback
   !
   vlan 1000
      rd 10.1.0.34:11000
      route-target both 65100:11000
      redistribute learned
   !
   vlan 2000
      rd 10.1.0.34:12000
      route-target both 12000:65000
      redistribute learned
   !
   address-family evpn
      neighbor EVPN_Overlay activate
!
router ospf 1
   router-id 10.0.0.33
   passive-interface default
   no passive-interface Ethernet1
   no passive-interface Ethernet2
   no passive-interface Ethernet3
   no passive-interface Port-Channel10
   max-lsa 12000
!
end
```

**Leaf1-3**

```
no aaa root
!
transceiver qsfp default-mode 4x10G
!
service routing protocols model multi-agent
!
hostname Leaf-3-Site-1
!
spanning-tree mode mstp
no spanning-tree vlan-id 4091
!
vlan 1000
   name OldVlan_client_1
!
vlan 2000
   name multisite-l2_2000
!
vlan 4091
   name MLAG-peer-Leaf2-1
   trunk group MLAG-peer_Server-1
!
vrf instance Keepalive
!
interface Port-Channel10
!
interface Port-Channel11
   description MLAG_Leaf-2
   switchport mode trunk
   switchport trunk group MLAG-peer_Server-1
   spanning-tree link-type point-to-point
!
interface Port-Channel20
   shutdown
!
interface Port-Channel22
   switchport trunk allowed vlan 1000,2000
   switchport mode trunk
   mlag 10
!
interface Ethernet1
   no switchport
   ip address 10.2.0.5/31
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.1
   ip ospf message-digest-key 10 md5 7 f3D/O8/wE3IGG+uuvYhESw==
!
interface Ethernet2
   shutdown
!
interface Ethernet3
   no switchport
   ip address 10.2.1.5/31
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.1
   ip ospf message-digest-key 10 md5 7 DYWREOc3d9i1hc3SA03m/g==
!
interface Ethernet4
!
interface Ethernet5
   no switchport
   vrf Keepalive
   ip address 10.3.0.1/31
!
interface Ethernet6
   description MLAG_Eth6_Leaf-2
   channel-group 11 mode active
!
interface Ethernet7
   description MLAG_Eth7_Leaf-2
   channel-group 11 mode active
!
interface Ethernet8
   channel-group 22 mode active
!
interface Ethernet9
!
interface Ethernet10
!
interface Loopback0
   ip address 10.0.0.34/32
   ip ospf area 0.0.0.1
!
interface Loopback1
   ip address 10.1.0.34/32
   ip ospf area 0.0.0.1
!
interface Management1
!
interface Vlan4091
   no autostate
   ip address 10.3.0.3/31
   ip ospf area 0.0.0.1
!
interface Vxlan1
   vxlan source-interface Loopback1
   vxlan udp-port 4789
   vxlan vlan 1000 vni 11000
   vxlan vlan 2000 vni 12000
   vxlan learn-restrict any
!
ip virtual-router mac-address c0:01:09:05:00:56
!
ip routing
ip routing vrf Keepalive
!
ip prefix-list Leaf_LB seq 10 permit 10.0.0.34/32
ip prefix-list Leaf_LB seq 20 permit 10.1.0.34/32
!
mlag configuration
   domain-id Leaf2-3-Site-1
   local-interface Vlan4091
   peer-address 10.3.0.2
   peer-address heartbeat 10.3.0.0 vrf Keepalive
   peer-link Port-Channel11
   dual-primary detection delay 10 action errdisable all-interfaces
!
route-map Loopback permit 10
   match ip address prefix-list Leaf_LB
!
router bgp 65100
   router-id 10.0.0.34
   maximum-paths 4 ecmp 64
   neighbor EVPN_Overlay peer group
   neighbor EVPN_Overlay remote-as 65100
   neighbor EVPN_Overlay update-source Loopback0
   neighbor EVPN_Overlay ebgp-multihop 10
   neighbor EVPN_Overlay timers 30 90
   neighbor EVPN_Overlay password 7 9aEh1CE6iI7OAWCvGTRLsw==
   neighbor EVPN_Overlay send-community extended
   neighbor 10.0.0.0 peer group EVPN_Overlay
   neighbor 10.0.0.1 peer group EVPN_Overlay
   redistribute connected route-map Loopback
   !
   vlan 1000
      rd 10.1.0.34:11000
      route-target both 65100:11000
      redistribute learned
   !
   vlan 2000
      rd 10.1.0.34:12000
      route-target both 12000:65000
      redistribute learned
   !
   address-family evpn
      neighbor EVPN_Overlay activate
!
router ospf 1
   router-id 10.0.0.34
   passive-interface default
   no passive-interface Ethernet1
   no passive-interface Ethernet2
   no passive-interface Ethernet3
   max-lsa 12000
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
vlan 2000
   name multisite-l2_2000
!
interface Port-Channel10
   switchport trunk allowed vlan 903,1000,2000
   switchport mode trunk
!
interface Ethernet1
   channel-group 10 mode active
!
interface Ethernet2
   channel-group 10 mode active
!
interface Ethernet3
   shutdown
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
interface Vlan2000
   ip address 192.168.200.1/24
!
ip routing
!
ip route 0.0.0.0/0 192.168.100.1
!
end
```