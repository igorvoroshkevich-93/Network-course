**Spine2-1**

```
no aaa root
!
transceiver qsfp default-mode 4x10G
!
service routing protocols model multi-agent
!
hostname Spine-1-Site-2
!
spanning-tree mode mstp
!
interface Ethernet1
   no switchport
   ip address 10.12.0.0/31
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.1
   ip ospf message-digest-key 10 md5 7 f3D/O8/wE3IGG+uuvYhESw==
!
interface Ethernet2
   no switchport
   ip address 10.12.0.2/31
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.1
   ip ospf message-digest-key 10 md5 7 DYWREOc3d9i1hc3SA03m/g==
!
interface Ethernet3
   no switchport
   ip address 10.12.0.4/31
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.1
   ip ospf message-digest-key 10 md5 7 DYWREOc3d9i1hc3SA03m/g==
!
interface Loopback0
   ip address 10.10.0.0/32
   ip ospf area 0.0.0.1
!
interface Management1
!
ip routing
!
ip prefix-list Leaf_LB seq 10 permit 10.10.0.32/32
ip prefix-list Leaf_LB seq 20 permit 10.10.0.33/32
ip prefix-list Leaf_LB seq 30 permit 10.10.0.34/32
ip prefix-list Leaf_LB seq 70 permit 10.11.0.32/32
ip prefix-list Leaf_LB seq 80 permit 10.11.0.34/32
ip prefix-list Spine_LB seq 10 permit 10.10.0.0/32
!
route-map Loopback permit 10
   match ip address prefix-list Spine_LB
!
route-map Loopback permit 20
   match ip address prefix-list Leaf_LB
!
peer-filter ASN_Leafs
   10 match as-range 65200-65299 result accept
!
router bgp 65200
   router-id 10.10.0.0
   maximum-paths 4 ecmp 64
   bgp listen range 10.10.0.0/24 peer-group EVPN_Overlay peer-filter ASN_Leafs
   neighbor EVPN_Overlay peer group
   neighbor EVPN_Overlay remote-as 65200
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
   router-id 10.10.0.0
   passive-interface default
   no passive-interface Ethernet1
   no passive-interface Ethernet2
   no passive-interface Ethernet3
   max-lsa 12000
!
end


```


**Leaf2-1**

```
no aaa root
!
transceiver qsfp default-mode 4x10G
!
service routing protocols model multi-agent
!
hostname Leaf-1-Site-2
!
spanning-tree mode mstp
!
vlan 1001
   name multisite-l3_1001
!
vlan 2000
   name multisite-l2_2000
!
vrf instance Multisite-1
!
interface Ethernet1
   no switchport
   ip address 10.12.0.1/31
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.1
   ip ospf message-digest-key 10 md5 7 f3D/O8/wE3IGG+uuvYhESw==
!
interface Ethernet3
   description DCI-1
   no switchport
   ip address 10.100.0.3/31
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
interface Loopback0
   ip address 10.10.0.32/32
   ip ospf area 0.0.0.1
!
interface Loopback1
   ip address 10.11.0.32/32
   ip ospf area 0.0.0.1
!
interface Management1
!
interface Vlan1001
   description Multisite-1_1001
   vrf Multisite-1
   ip address virtual 192.168.101.1/24
!
interface Vxlan1
   vxlan source-interface Loopback1
   vxlan udp-port 4789
   vxlan vlan 1001 vni 11001
   vxlan vlan 2000 vni 12000
   vxlan vrf Multisite-1 vni 20001
   vxlan learn-restrict any
!
ip routing
ip routing vrf Multisite-1
!
ip prefix-list Leaf_LB seq 10 permit 10.10.0.32/32
ip prefix-list Leaf_LB seq 20 permit 10.11.0.32/32
!
route-map Loopback permit 10
   match ip address prefix-list Leaf_LB
!
router bgp 65200
   router-id 10.10.0.32
   maximum-paths 4 ecmp 64
   neighbor EVPN_Overlay peer group
   neighbor EVPN_Overlay remote-as 65200
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
   neighbor 10.10.0.0 peer group EVPN_Overlay
   neighbor 10.100.0.2 peer group underlay-export-local
   neighbor 10.100.0.2 remote-as 65000
   neighbor 10.100.0.2 description dci-r1
   neighbor 10.101.0.99 peer group evpn-dci-peers
   neighbor 10.101.0.99 remote-as 65000
   neighbor 10.101.0.99 description dci-r1
   redistribute connected route-map all-loopbacks
   !
   vlan 1001
      rd 10.10.0.32:11001
      route-target both 65200:11001
      redistribute learned
   !
   vlan 2000
      rd evpn domain all 10.10.0.32:12000
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
      rd 10.10.0.32:20001
      route-target import evpn 20001:65000
      route-target export evpn 20001:65000
      redistribute connected
!
router ospf 1
   router-id 10.10.0.32
   passive-interface default
   no passive-interface Ethernet1
   max-lsa 12000
!
end


```

**Leaf2-2**

```
no aaa root
!
transceiver qsfp default-mode 4x10G
!
service routing protocols model multi-agent
!
link tracking group EVPN-ESI-MH
   recovery delay 60
!
hostname Leaf-2-Site-2
!
spanning-tree mode mstp
!
vlan 1001
   name vlan1001_Client2
!
vlan 2000
   name multisite-l2_2000
!
interface Port-Channel20
   description to-Client-2
   switchport trunk allowed vlan 1001,2000
   switchport mode trunk
   !
   evpn ethernet-segment
      identifier 0000:0000:0905:0000:0056
      route-target import 00:00:09:05:00:56
   lacp system-id 0000.0905.0056
   link tracking group EVPN-ESI-MH downstream
!
interface Ethernet1
   description to-Spine-1-Site-2
   no switchport
   ip address 10.12.0.3/31
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.1
   ip ospf message-digest-key 10 md5 7 f3D/O8/wE3IGG+uuvYhESw==
   link tracking group EVPN-ESI-MH upstream
!
interface Ethernet3
   channel-group 20 mode active
!
interface Loopback0
   ip address 10.10.0.33/32
   ip ospf area 0.0.0.1
!
interface Loopback1
   ip address 10.11.0.34/32
   ip ospf area 0.0.0.1
!
interface Management1
!
interface Vxlan1
   vxlan source-interface Loopback1
   vxlan udp-port 4789
   vxlan vlan 1001 vni 11001
   vxlan vlan 2000 vni 12000
   vxlan learn-restrict any
!
ip virtual-router mac-address c0:01:09:08:00:56
!
ip routing
!
ip prefix-list Leaf_LB seq 10 permit 10.10.0.33/32
ip prefix-list Leaf_LB seq 20 permit 10.11.0.34/32
!
route-map Loopback permit 10
   match ip address prefix-list Leaf_LB
!
router bgp 65200
   router-id 10.10.0.33
   maximum-paths 4 ecmp 64
   neighbor EVPN_Overlay peer group
   neighbor EVPN_Overlay remote-as 65200
   neighbor EVPN_Overlay update-source Loopback0
   neighbor EVPN_Overlay ebgp-multihop 10
   neighbor EVPN_Overlay timers 30 90
   neighbor EVPN_Overlay password 7 9aEh1CE6iI7OAWCvGTRLsw==
   neighbor EVPN_Overlay send-community extended
   neighbor 10.10.0.0 peer group EVPN_Overlay
   redistribute connected route-map Loopback
   !
   vlan 1001
      rd 10.11.0.34:11001
      route-target both 65200:11001
      redistribute learned
   !
   vlan 2000
      rd 10.11.0.34:12000
      route-target both 12000:65000
      redistribute learned
   !
   address-family evpn
      neighbor EVPN_Overlay activate
!
router ospf 1
   router-id 10.10.0.33
   passive-interface default
   no passive-interface Ethernet1
   no passive-interface Port-Channel10
   max-lsa 12000
!
end


```

**Leaf2-3**

```
no aaa root
!
transceiver qsfp default-mode 4x10G
!
service routing protocols model multi-agent
!
link tracking group EVPN-ESI-MH
   recovery delay 60
!
hostname Leaf-3-Site-2
!
spanning-tree mode mstp
!
vlan 1001
   name Vlan-1001-Client-2
!
vlan 2000
   name multisite-l2_2000
!
vrf instance Otus_Symmetric_L3
!
interface Port-Channel20
   description to-Client-2
   switchport trunk allowed vlan 1001,2000
   switchport mode trunk
   !
   evpn ethernet-segment
      identifier 0000:0000:0905:0000:0056
      designated-forwarder election algorithm preference 110
      route-target import 00:00:09:05:00:56
   lacp system-id 0000.0905.0056
   link tracking group EVPN-ESI-MH downstream
!
interface Ethernet1
   description to-Spine-1-Site-2
   no switchport
   ip address 10.12.0.5/31
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.1
   ip ospf message-digest-key 10 md5 7 f3D/O8/wE3IGG+uuvYhESw==
   link tracking group EVPN-ESI-MH upstream
!
interface Ethernet3
   channel-group 20 mode active
!
interface Loopback0
   ip address 10.10.0.34/32
   ip ospf area 0.0.0.1
!
interface Loopback1
   ip address 10.11.0.34/32
   ip ospf area 0.0.0.1
!
interface Management1
!
interface Vxlan1
   vxlan source-interface Loopback1
   vxlan udp-port 4789
   vxlan vlan 1001 vni 11001
   vxlan vlan 2000 vni 12000
   vxlan learn-restrict any
!
ip virtual-router mac-address c0:01:09:08:00:56
!
ip routing
!
ip prefix-list Leaf_LB seq 10 permit 10.10.0.34/32
ip prefix-list Leaf_LB seq 20 permit 10.11.0.34/32
!
route-map Loopback permit 10
   match ip address prefix-list Leaf_LB
!
router bgp 65200
   router-id 10.10.0.34
   maximum-paths 4 ecmp 64
   neighbor EVPN_Overlay peer group
   neighbor EVPN_Overlay remote-as 65200
   neighbor EVPN_Overlay update-source Loopback0
   neighbor EVPN_Overlay ebgp-multihop 10
   neighbor EVPN_Overlay timers 30 90
   neighbor EVPN_Overlay password 7 9aEh1CE6iI7OAWCvGTRLsw==
   neighbor EVPN_Overlay send-community extended
   neighbor 10.10.0.0 peer group EVPN_Overlay
   redistribute connected route-map Loopback
   !
   vlan 1001
      rd 10.11.0.34:11001
      route-target both 65200:11001
      redistribute learned
   !
   vlan 2000
      rd 10.11.0.34:12000
      route-target both 12000:65000
      redistribute learned
   !
   address-family evpn
      neighbor EVPN_Overlay activate
!
router ospf 1
   router-id 10.10.0.34
   passive-interface default
   no passive-interface Ethernet1
   no passive-interface Ethernet2
   no passive-interface Port-Channel10
   max-lsa 12000
!
end
```

**Client-2**

```
no aaa root
!
transceiver qsfp default-mode 4x10G
!
service routing protocols model multi-agent
!
hostname Client-2
!
spanning-tree mode mstp
!
vlan 1001
   name Client-1_OLD
!
vlan 2000
   name multisite-l2_2000
!
interface Port-Channel10
   switchport trunk allowed vlan 1001,2000
   switchport mode trunk
!
interface Ethernet1
   channel-group 10 mode active
!
interface Ethernet2
   channel-group 10 mode active
!
interface Vlan1001
   ip address 192.168.101.2/24
!
interface Vlan2000
   ip address 192.168.200.2/24
!
ip routing
!
ip route 0.0.0.0/0 192.168.101.1
!
end


```