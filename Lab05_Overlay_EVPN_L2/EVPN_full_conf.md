**Spine-1**

```
hostname Spine-1

!
router bgp 65100
   router-id 10.1.0.0
   maximum-paths 2 ecmp 2
   neighbor EVPN_Overlay peer group
   neighbor EVPN_Overlay remote-as 65100
   neighbor EVPN_Overlay update-source Loopback1
   neighbor EVPN_Overlay route-reflector-client
   neighbor EVPN_Overlay send-community extended
   neighbor EVPN_Overlay bfd
   neighbor EVPN_Overlay timers 3 9
   neighbor EVPN_Overlay password Otus_Overlay
   neighbor 10.0.0.32 peer group EVPN_Overlay
   neighbor 10.0.0.33 peer group EVPN_Overlay
   neighbor 10.0.0.34 peer group EVPN_Overlay
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
hostname Spine-2
!
router bgp 65100
   router-id 10.1.0.1
   maximum-paths 2 ecmp 2
   neighbor EVPN_Overlay peer group
   neighbor EVPN_Overlay remote-as 65100
   neighbor EVPN_Overlay update-source Loopback1
   neighbor EVPN_Overlay route-reflector-client
   neighbor EVPN_Overlay send-community extended
   neighbor EVPN_Overlay bfd
   neighbor EVPN_Overlay timers 3 9
   neighbor EVPN_Overlay password Otus_Overlay
   neighbor 10.0.0.32 peer group EVPN_Overlay
   neighbor 10.0.0.33 peer group EVPN_Overlay
   neighbor 10.0.0.34 peer group EVPN_Overlay
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
hostname Leaf-1
!
vlan 903
   name Clients_903
!
Interface EthernetX
   switchport mode access
   switchport access vlan 903

!
router bgp 65100
   router-id 10.1.0.32
   maximum-paths 2 ecmp 2
   neighbor EVPN_Overlay peer group
   neighbor EVPN_Overlay remote-as 65100
   neighbor EVPN_Overlay update-source Loopback1
   neighbor EVPN_Overlay send-community extended
   neighbor EVPN_Overlay bfd
   neighbor EVPN_Overlay timers 3 9
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
      route-target both evpn 65100:109030
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
hostname Leaf-2
!
vlan 904
   name Clients_904
!
Interface EthernetY
   switchport mode access
   switchport access vlan 904

!
router bgp 65100
   router-id 10.1.0.33
   maximum-paths 2 ecmp 2
   neighbor EVPN_Overlay peer group
   neighbor EVPN_Overlay remote-as 65100
   neighbor EVPN_Overlay update-source Loopback1
   neighbor EVPN_Overlay send-community extended
   neighbor EVPN_Overlay bfd
   neighbor EVPN_Overlay timers 3 9
   neighbor EVPN_Overlay password Otus_Overlay
   neighbor 10.0.0.0 peer group EVPN_Overlay
   neighbor 10.0.0.1 peer group EVPN_Overlay
   redistribute connected route-map Loopback
   !
   address-family evpn
      neighbor EVPN_Overlay activate
   !
   vlan 904
      rd 10.1.0.33:10904
      route-target both evpn 65100:109040
      redistribute learned


!
int vxlan1
  vxlan source-int lo1
  vxlan udp-port 4789
  vxlan learn-restrict any
  vxlan vlan 904 vni 10904
!
```

**Leaf-3**

```
hostname Leaf-3
!
vlan 903
   name Clients_903
!
vlan 904
   name Clients_904
!
Interface EthernetX
   switchport mode access
   switchport access vlan 903

Interface EthernetY
   switchport mode access
   switchport access vlan 904

!
router bgp 65100
   router-id 10.1.0.34
   maximum-paths 2 ecmp 2
   neighbor EVPN_Overlay peer group
   neighbor EVPN_Overlay remote-as 65100
   neighbor EVPN_Overlay update-source Loopback1
   neighbor EVPN_Overlay send-community extended
   neighbor EVPN_Overlay bfd
   neighbor EVPN_Overlay timers 3 9
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
      route-target both evpn 65100:109030
      redistribute learned
   vlan 904
      rd 10.1.0.34:10904
      route-target both evpn 65100:109040
      redistribute learned


!
int vxlan1
  vxlan source-int lo1
  vxlan udp-port 4789
  vxlan learn-restrict any
  vxlan vlan 903 vni 10903
  vxlan vlan 904 vni 10904
!
```

**Client-1**

```

```

**Client-2**

```

```

**Client-3**

```

```

**Client-4**

```

```
