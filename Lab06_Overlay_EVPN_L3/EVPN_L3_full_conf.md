**Spine-1**

```
service routing protocols model multi-agent

!
ip prefix-list Leaf_LB seq 10 permit 10.0.0.32/32
ip prefix-list Leaf_LB seq 20 permit 10.0.0.33/32
ip prefix-list Leaf_LB seq 30 permit 10.0.0.34/32
ip prefix-list Leaf_LB seq 40 permit 10.1.0.32/32
ip prefix-list Leaf_LB seq 50 permit 10.1.0.33/32
ip prefix-list Leaf_LB seq 60 permit 10.1.0.34/32
ip prefix-list Spine_LB seq 10 permit 10.0.0.0/32
ip prefix-list Spine_LB seq 20 permit 10.1.0.0/32
!
route-map Loopback permit 10
   match ip address prefix-list Spine_LB
!
route-map Loopback permit 20
   match ip address prefix-list Leaf_LB
!
peer-filter ASN_Leafs
   10 match as-range 65101-65199 result accept
!
router bgp 65100
   router-id 10.1.0.0
   maximum-paths 2 ecmp 2
   bgp listen range 10.1.0.0/24 peer-group EVPN_Overlay peer-filter ASN_Leafs
   neighbor EVPN_Overlay peer group
   neighbor EVPN_Overlay remote-as 65100
   neighbor EVPN_Overlay update-source Loopback1
   neighbor EVPN_Overlay route-reflector-client
   neighbor EVPN_Overlay send-community extended
   neighbor EVPN_Overlay ebgp-multihop 10
   neighbor EVPN_Overlay bfd 
   neighbor EVPN_Overlay bfd interval 300 min-rx 300 multiplier 3
   neighbor EVPN_Overlay timers 3 9
   neighbor EVPN_Overlay password Otus_Overlay
   redistribute connected route-map Loopback
   !
   address-family evpn
      neighbor EVPN_Overlay activate
   !
!
end
```

**Spine-2**

```
service routing protocols model multi-agent

!
ip prefix-list Leaf_LB seq 10 permit 10.0.0.32/32
ip prefix-list Leaf_LB seq 20 permit 10.0.0.33/32
ip prefix-list Leaf_LB seq 30 permit 10.0.0.34/32
ip prefix-list Leaf_LB seq 40 permit 10.1.0.32/32
ip prefix-list Leaf_LB seq 50 permit 10.1.0.33/32
ip prefix-list Leaf_LB seq 60 permit 10.1.0.34/32
ip prefix-list Spine_LB seq 10 permit 10.0.0.0/32
ip prefix-list Spine_LB seq 20 permit 10.1.0.0/32
!
route-map Loopback permit 10
   match ip address prefix-list Spine_LB
!
route-map Loopback permit 20
   match ip address prefix-list Leaf_LB
!
peer-filter ASN_Leafs
   10 match as-range 65101-65199 result accept
!
router bgp 65100
   router-id 10.1.0.1
   maximum-paths 2 ecmp 2
   bgp listen range 10.1.0.0/24 peer-group EVPN_Overlay peer-filter ASN_Leafs
   neighbor EVPN_Overlay peer group
   neighbor EVPN_Overlay remote-as 65100
   neighbor EVPN_Overlay update-source Loopback1
   neighbor EVPN_Overlay route-reflector-client
   neighbor EVPN_Overlay send-community extended
   neighbor EVPN_Overlay ebgp-multihop 10
   neighbor EVPN_Overlay bfd 
   neighbor EVPN_Overlay bfd interval 300 min-rx 300 multiplier 3
   neighbor EVPN_Overlay timers 3 9
   neighbor EVPN_Overlay password Otus_Overlay
   redistribute connected route-map Loopback
   !
   address-family evpn
      neighbor EVPN_Overlay activate
   !
!
end
```



**Leaf-1**

```
service routing protocols model multi-agent
!
vlan 903
   name Clients_903
!
vrf instance Otus_Symmetric_L3
!
ip routing vrf Otus_Symmetric_L3
!
int vlan 903
   vrf Otus_Symmetric_L3
   ip virtual address 172.16.0.1/24
!
Interface Ethernet7
   switchport mode access
   switchport access vlan 903

!
router bgp 65101
   router-id 10.1.0.32
   maximum-paths 2 ecmp 2
   neighbor EVPN_Overlay peer group
   neighbor EVPN_Overlay remote-as 65100
   neighbor EVPN_Overlay update-source Loopback1
   neighbor EVPN_Overlay send-community extended
   neighbor EVPN_Overlay ebgp-multihop 10
   neighbor EVPN_Overlay bfd 
   neighbor EVPN_Overlay bfd interval 300 min-rx 300 multiplier 3
   neighbor EVPN_Overlay timers 3 9
   neighbor EVPN_Overlay password Otus_Overlay
   neighbor 10.1.0.0 peer group EVPN_Overlay
   neighbor 10.1.0.1 peer group EVPN_Overlay
   redistribute connected route-map Loopback
   !
   address-family evpn
      neighbor EVPN_Overlay activate
   !
   vlan 903
      rd 10.1.0.32:10903
      route-target both evpn 65100:10903
      redistribute learned
   vrf Otus_Symmetric_L3
      rd 10.1.0.32:10999
      route-target import evpn 65100:10999
      route-target export evpn 65100:10999
      redistribute learned
      redistribute connected


!
int vxlan1
  vxlan source-int lo1
  vxlan udp-port 4789
  vxlan learn-restrict any
  vxlan vlan 903 vni 10903
  vxlan vrf Otus_Symmetric_L3 vni 10999
!
```

**Leaf-2**

```
service routing protocols model multi-agent
!
vlan 904
   name Clients_904
!
vrf instance Otus_Symmetric_L3
!
ip routing vrf Otus_Symmetric_L3
!
int vlan 904
   vrf Otus_Symmetric_L3
   ip virtual address 172.16.1.1/24
!
Interface Ethernet7
   switchport mode access
   switchport access vlan 904

!
router bgp 65102
   router-id 10.1.0.33
   maximum-paths 2 ecmp 2
   neighbor EVPN_Overlay peer group
   neighbor EVPN_Overlay remote-as 65100
   neighbor EVPN_Overlay update-source Loopback1
   neighbor EVPN_Overlay send-community extended
   neighbor EVPN_Overlay ebgp-multihop 10
   neighbor EVPN_Overlay bfd 
   neighbor EVPN_Overlay bfd interval 300 min-rx 300 multiplier 3
   neighbor EVPN_Overlay timers 3 9
   neighbor EVPN_Overlay password Otus_Overlay
   neighbor 10.1.0.0 peer group EVPN_Overlay
   neighbor 10.1.0.1 peer group EVPN_Overlay
   redistribute connected route-map Loopback
   !
   address-family evpn
      neighbor EVPN_Overlay activate
   !
   vlan 904
      rd 10.1.0.33:10904
      route-target both evpn 65100:10904
      redistribute learned
   vrf Otus_Symmetric_L3
      rd 10.1.0.33:10999
      route-target import evpn 65100:10999
      route-target export evpn 65100:10999
      redistribute learned
      redistribute connected


!
int vxlan1
  vxlan source-int lo1
  vxlan udp-port 4789
  vxlan learn-restrict any
  vxlan vlan 904 vni 109040
  vxlan vrf Otus_Symmetric_L3 vni 10999
!
```

**Leaf-3**

```
service routing protocols model multi-agent
!
vlan 903
   name Clients_905
!
vlan 904
   name Clients_906
!
vrf instance Otus_Symmetric_L3
!
ip routing vrf Otus_Symmetric_L3
!
int vlan 905
   vrf Otus_Symmetric_L3
   ip virtual address 172.16.2.1/24
!
int vlan 906
   vrf Otus_Symmetric_L3
   ip virtual address 172.16.3.1/24
!
Interface Ethernet7
   switchport mode access
   switchport access vlan 905
!
Interface Ethernet8
   switchport mode access
   switchport access vlan 906

!
router bgp 65103
   router-id 10.1.0.34
   maximum-paths 2 ecmp 2
   neighbor EVPN_Overlay peer group
   neighbor EVPN_Overlay remote-as 65100
   neighbor EVPN_Overlay update-source Loopback1
   neighbor EVPN_Overlay send-community extended
   neighbor EVPN_Overlay ebgp-multihop 10
   neighbor EVPN_Overlay bfd 
   neighbor EVPN_Overlay bfd interval 300 min-rx 300 multiplier 3
   neighbor EVPN_Overlay timers 3 9
   neighbor EVPN_Overlay password Otus_Overlay
   neighbor 10.0.0.0 peer group EVPN_Overlay
   neighbor 10.0.0.1 peer group EVPN_Overlay
   redistribute connected route-map Loopback
   !
   address-family evpn
      neighbor EVPN_Overlay activate
   !
   vlan 905
      rd 10.1.0.34:10905
      route-target both evpn 65100:10905
      redistribute learned
   vlan 906
      rd 10.1.0.34:10906
      route-target both evpn 65100:10906
      redistribute learned
   vrf Otus_Symmetric_L3
      rd 10.1.0.34:10999
      route-target import evpn 65100:10999
      route-target export evpn 65100:10999
      redistribute learned
      redistribute connected



!
int vxlan1
  vxlan source-int lo1
  vxlan udp-port 4789
  vxlan learn-restrict any
  vxlan vlan 905 vni 10905
  vxlan vlan 906 vni 10906
  vxlan vrf Otus_Symmetric_L3 vni 10999
!
```

**Client-1**

```
ip address 172.16.0.2/24 172.16.0.1
```

**Client-2**

```
ip address 172.16.1.2/24 172.16.1.1
```

**Client-3**

```
ip address 172.16.2.2/24 172.16.2.1
```

**Client-4**

```
ip address 172.16.3.2/24 172.16.3.1
```
