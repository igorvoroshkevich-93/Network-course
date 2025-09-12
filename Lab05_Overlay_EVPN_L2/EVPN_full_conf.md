**Spine-1**

```
service routing protocols model multi-agent

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
service routing protocols model multi-agent
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
service routing protocols model multi-agent
!
vlan 903
   name Clients_903
!
Interface Ethernet7
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
      rd 10.1.0.32:109030
      route-target both evpn 65100:109030
      redistribute learned


!
int vxlan1
  vxlan source-int lo1
  vxlan udp-port 4789
  vxlan learn-restrict any
  vxlan vlan 903 vni 109030
!
```

**Leaf-2**

```
service routing protocols model multi-agent
!
vlan 904
   name Clients_904
!
Interface Ethernet7
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
      rd 10.1.0.33:109040
      route-target both evpn 65100:109040
      redistribute learned


!
int vxlan1
  vxlan source-int lo1
  vxlan udp-port 4789
  vxlan learn-restrict any
  vxlan vlan 904 vni 109040
!
```

**Leaf-3**

```
service routing protocols model multi-agent
!
vlan 903
   name Clients_903
!
vlan 904
   name Clients_904
!
Interface Ethernet7
   switchport mode access
   switchport access vlan 903

Interface Ethernet8
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
      rd 10.1.0.34:109030
      route-target both evpn 65100:109030
      redistribute learned
   vlan 904
      rd 10.1.0.34:109040
      route-target both evpn 65100:109040
      redistribute learned


!
int vxlan1
  vxlan source-int lo1
  vxlan udp-port 4789
  vxlan learn-restrict any
  vxlan vlan 903 vni 109030
  vxlan vlan 904 vni 109040
!
```

**Client-1**

```
ip address 172.16.0.2/24
```

**Client-2**

```
ip address 172.16.1.2/24
```

**Client-3**

```
ip address 172.16.0.3/24
```

**Client-4**

```
ip address 172.16.1.3/24
```
