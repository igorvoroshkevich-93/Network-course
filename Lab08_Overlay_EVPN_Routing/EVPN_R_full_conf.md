**Spine-1**

```
service routing protocols model multi-agent
!
ip prefix-list Leaf_LB seq 10 permit 10.0.0.32/32
ip prefix-list Leaf_LB seq 20 permit 10.0.0.33/32
ip prefix-list Leaf_LB seq 30 permit 10.0.0.34/32
ip prefix-list Leaf_LB seq 40 permit 10.0.0.35/32
ip prefix-list Leaf_LB seq 50 permit 10.0.0.36/32
ip prefix-list Leaf_LB seq 60 permit 10.0.0.37/32
ip prefix-list Leaf_LB seq 70 permit 10.1.0.32/32
ip prefix-list Leaf_LB seq 80 permit 10.1.0.34/32
ip prefix-list Leaf_LB seq 90 permit 10.1.0.35/32
ip prefix-list Leaf_LB seq 100 permit 10.1.0.36/32
ip prefix-list Leaf_LB seq 110 permit 10.1.0.37/32
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
   router-id 10.0.0.0
   maximum-paths 4 ecmp 64
   bgp listen range 10.0.0.0/24 peer-group EVPN_Overlay peer-filter ASN_Leafs
   neighbor EVPN_Overlay peer group
   neighbor EVPN_Overlay remote-as 65100
   neighbor EVPN_Overlay update-source Loopback0
   neighbor EVPN_Overlay route-reflector-client
   neighbor EVPN_Overlay send-community extended
   neighbor EVPN_Overlay ebgp-multihop 10
   neighbor EVPN_Overlay timers 30 90
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
ip prefix-list Leaf_LB seq 40 permit 10.0.0.35/32
ip prefix-list Leaf_LB seq 50 permit 10.0.0.36/32
ip prefix-list Leaf_LB seq 60 permit 10.0.0.37/32
ip prefix-list Leaf_LB seq 70 permit 10.1.0.32/32
ip prefix-list Leaf_LB seq 80 permit 10.1.0.34/32
ip prefix-list Leaf_LB seq 90 permit 10.1.0.35/32
ip prefix-list Leaf_LB seq 100 permit 10.1.0.36/32
ip prefix-list Leaf_LB seq 110 permit 10.1.0.37/32
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
   router-id 10.0.0.1
   maximum-paths 4 ecmp 64
   bgp listen range 10.0.0.0/24 peer-group EVPN_Overlay peer-filter ASN_Leafs
   neighbor EVPN_Overlay peer group
   neighbor EVPN_Overlay remote-as 65100
   neighbor EVPN_Overlay update-source Loopback0
   neighbor EVPN_Overlay route-reflector-client
   neighbor EVPN_Overlay send-community extended
   neighbor EVPN_Overlay ebgp-multihop 10
   neighbor EVPN_Overlay timers 30 90
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
ip prefix-list Leaf_LB seq 10 permit 10.0.0.32/32
ip prefix-list Leaf_LB seq 20 permit 10.1.0.32/32
!
route-map Loopback permit 10
   match ip address prefix-list Leaf_LB
!
router bgp 65112
   router-id 10.0.0.32
   maximum-paths 4 ecmp 64
   neighbor EVPN_Overlay peer group
   neighbor EVPN_Overlay remote-as 65100
   neighbor EVPN_Overlay update-source Loopback0
   neighbor EVPN_Overlay send-community extended
   neighbor EVPN_Overlay ebgp-multihop 10
   neighbor EVPN_Overlay timers 30 90
   neighbor EVPN_Overlay password Otus_Overlay
   neighbor 10.0.0.0 peer group EVPN_Overlay
   neighbor 10.0.0.1 peer group EVPN_Overlay
   redistribute connected route-map Loopback
   !
   address-family evpn
      neighbor EVPN_Overlay activate
   !
   vlan 903
      rd 10.1.0.32:10903
      route-target both 65100:10903
      redistribute learned
!
int vxlan1
  vxlan source-int lo1
  vxlan udp-port 4789
  vxlan learn-restrict any
  vxlan vlan 903 vni 10903
!
```

**Leaf-2**

```
service routing protocols model multi-agent
!
ip prefix-list Leaf_LB seq 10 permit 10.0.0.33/32
ip prefix-list Leaf_LB seq 20 permit 10.1.0.32/32
!
route-map Loopback permit 10
   match ip address prefix-list Leaf_LB
!
router bgp 65112
   router-id 10.0.0.33
   maximum-paths 4 ecmp 64
   neighbor EVPN_Overlay peer group
   neighbor EVPN_Overlay remote-as 65100
   neighbor EVPN_Overlay update-source Loopback0
   neighbor EVPN_Overlay send-community extended
   neighbor EVPN_Overlay ebgp-multihop 10
   neighbor EVPN_Overlay timers 30 90
   neighbor EVPN_Overlay password Otus_Overlay
   neighbor 10.0.0.0 peer group EVPN_Overlay
   neighbor 10.0.0.1 peer group EVPN_Overlay
   redistribute connected route-map Loopback
   !
   address-family evpn
      neighbor EVPN_Overlay activate
   !
   vlan 903
      rd 10.1.0.33:10903
      route-target both 65100:10903
      redistribute learned
!
int vxlan1
  vxlan source-int lo1
  vxlan udp-port 4789
  vxlan learn-restrict any
  vxlan vlan 903 vni 10903
!
```


**Leaf-3**

```
service routing protocols model multi-agent
!
vlan 903
   name Otus_BLUE
!
vlan 905
   name Otus_RED
!
vrf instance Otus_BLUE
!
vrf instance Otus_RED
!
interface Vlan903
   vrf Otus_BLUE
   ip address virtual 172.16.0.1/24
   mac address virtual-router
!
interface Vlan905
   vrf Otus_RED
   ip address virtual 172.16.2.1/24
   mac address virtual-router
!
ip virtual-router mac-address c0:01:09:05:00:56
!
ip routing vrf Otus_BLUE
ip routing vrf Otus_RED
!
ip prefix-list Leaf_LB seq 10 permit 10.0.0.34/32
ip prefix-list Leaf_LB seq 20 permit 10.1.0.34/32
!
route-map Loopback permit 10
   match ip address prefix-list Leaf_LB
!
router bgp 65103
   router-id 10.0.0.34
   maximum-paths 4 ecmp 64
   neighbor EVPN_Overlay peer group
   neighbor EVPN_Overlay remote-as 65100
   neighbor EVPN_Overlay update-source Loopback0
   neighbor EVPN_Overlay send-community extended
   neighbor EVPN_Overlay ebgp-multihop 10
   neighbor EVPN_Overlay timers 30 90
   neighbor EVPN_Overlay password Otus_Overlay
   neighbor 10.0.0.0 peer group EVPN_Overlay
   neighbor 10.0.0.1 peer group EVPN_Overlay
   redistribute connected route-map Loopback
   !
   address-family evpn
      neighbor EVPN_Overlay activate
   !
   vlan 903
      rd 10.1.0.34:10903
      route-target both 65100:10903
      redistribute learned
   vlan 905
      rd 10.1.0.34:10905
      route-target both 65100:10905
      redistribute learned
   vrf Otus_BLUE
      rd 10.1.0.34:15001
      route-target import evpn 65100:15001
      route-target export evpn 65100:15001
      redistribute connected
   vrf Otus_RED
      rd 10.1.0.34:15002
      route-target import evpn 65100:15002
      route-target export evpn 65100:15002
      redistribute connected

!
int vxlan1
  vxlan source-int lo1
  vxlan udp-port 4789
  vxlan learn-restrict any
  vxlan vlan 903 vni 10903
  vxlan vlan 905 vni 10905
  vxlan vrf Otus_BLUE vni 15001
  vxlan vrf Otus_RED vni 15002
!
```

**Leaf-4**

```
service routing protocols model multi-agent
!
vlan 903
   name Otus_BLUE
!
vlan 905
   name Otus_RED
!
vrf instance Otus_BLUE
!
vrf instance Otus_RED
!
interface Vlan903
   vrf Otus_BLUE
   ip address virtual 172.16.0.1/24
   mac address virtual-router
!
interface Vlan905
   vrf Otus_RED
   ip address virtual 172.16.2.1/24
   mac address virtual-router
!
ip virtual-router mac-address c0:01:09:05:00:56
!
ip routing vrf Otus_BLUE
ip routing vrf Otus_RED
!
ip prefix-list Leaf_LB seq 10 permit 10.0.0.35/32
ip prefix-list Leaf_LB seq 20 permit 10.1.0.35/32
!
route-map Loopback permit 10
   match ip address prefix-list Leaf_LB
!
router bgp 65104
   router-id 10.0.0.35
   maximum-paths 4 ecmp 64
   neighbor EVPN_Overlay peer group
   neighbor EVPN_Overlay remote-as 65100
   neighbor EVPN_Overlay update-source Loopback0
   neighbor EVPN_Overlay send-community extended
   neighbor EVPN_Overlay ebgp-multihop 10
   neighbor EVPN_Overlay timers 30 90
   neighbor EVPN_Overlay password Otus_Overlay
   neighbor 10.0.0.0 peer group EVPN_Overlay
   neighbor 10.0.0.1 peer group EVPN_Overlay
   redistribute connected route-map Loopback
   !
   address-family evpn
      neighbor EVPN_Overlay activate
   !
   vlan 903
      rd 10.1.0.35:10903
      route-target both 65100:10903
      redistribute learned
   vlan 905
      rd 10.1.0.35:10905
      route-target both 65100:10905
      redistribute learned
   vrf Otus_BLUE
      rd 10.1.0.35:15001
      route-target import evpn 65100:15001
      route-target export evpn 65100:15001
      redistribute connected
   vrf Otus_RED
      rd 10.1.0.35:15002
      route-target import evpn 65100:15002
      route-target export evpn 65100:15002
      redistribute connected
!
int vxlan1
  vxlan source-int lo1
  vxlan udp-port 4789
  vxlan learn-restrict any
  vxlan vlan 903 vni 10903
  vxlan vlan 905 vni 10905
  vxlan vrf Otus_BLUE vni 15001
  vxlan vrf Otus_RED vni 15002
!
```

**Leaf-5**

```
service routing protocols model multi-agent
!
ip prefix-list Leaf_LB seq 10 permit 10.0.0.36/32
ip prefix-list Leaf_LB seq 20 permit 10.1.0.36/32
!
route-map Loopback permit 10
   match ip address prefix-list Leaf_LB
!
router bgp 65105
   router-id 10.0.0.36
   maximum-paths 4 ecmp 64
   neighbor EVPN_Overlay peer group
   neighbor EVPN_Overlay remote-as 65100
   neighbor EVPN_Overlay update-source Loopback0
   neighbor EVPN_Overlay send-community extended
   neighbor EVPN_Overlay ebgp-multihop 10
   neighbor EVPN_Overlay timers 30 90
   neighbor EVPN_Overlay password Otus_Overlay
   neighbor 10.0.0.0 peer group EVPN_Overlay
   neighbor 10.0.0.1 peer group EVPN_Overlay
   redistribute connected route-map Loopback
   !
   address-family evpn
      neighbor EVPN_Overlay activate
   !
   vlan 905
      rd 10.1.0.36:10905
      route-target both 65100:10905
      redistribute learned
!
int vxlan1
  vxlan source-int lo1
  vxlan udp-port 4789
  vxlan learn-restrict any
  vxlan vlan 905 vni 10905
!

```

**Leaf-6**

```
service routing protocols model multi-agent
!
ip prefix-list Leaf_LB seq 10 permit 10.0.0.37/32
ip prefix-list Leaf_LB seq 20 permit 10.1.0.37/32
!
route-map Loopback permit 10
   match ip address prefix-list Leaf_LB
!
router bgp 65106
   router-id 10.0.0.37
   maximum-paths 4 ecmp 64
   neighbor EVPN_Overlay peer group
   neighbor EVPN_Overlay remote-as 65100
   neighbor EVPN_Overlay update-source Loopback0
   neighbor EVPN_Overlay send-community extended
   neighbor EVPN_Overlay ebgp-multihop 10
   neighbor EVPN_Overlay timers 30 90
   neighbor EVPN_Overlay password Otus_Overlay
   neighbor 10.0.0.0 peer group EVPN_Overlay
   neighbor 10.0.0.1 peer group EVPN_Overlay
   redistribute connected route-map Loopback
   !
   address-family evpn
      neighbor EVPN_Overlay activate
   !
   vlan 905
      rd 10.1.0.37:10905
      route-target both 65100:10905
      redistribute learned
!
int vxlan1
  vxlan source-int lo1
  vxlan udp-port 4789
  vxlan learn-restrict any
  vxlan vlan 905 vni 10905
!

```

