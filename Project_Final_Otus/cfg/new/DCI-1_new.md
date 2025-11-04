**DCI-1**

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
interface Ethernet1
   description dc1-l1
   no switchport
   ip address 10.100.0.0/31
!
interface Ethernet2
   description dc2-l1
   no switchport
   ip address 10.100.0.2/31
!
interface Ethernet3
   description dc3-l1
   no switchport
   ip address 10.100.0.4/31
!
interface Loopback0
   ip address 10.101.0.99/32
!
interface Management1
!
ip routing
!
ip prefix-list loopback-routes
   seq 10 permit 10.0.0.0/8 ge 32
!
route-map all-loopbacks permit 10
   match ip address prefix-list loopback-routes
!
route-map local-loopbacks permit 10
   match ip address prefix-list loopback-routes
   match source-protocol connected
!
router bgp 65000
   router-id 10.101.0.99
   no bgp default ipv4-unicast
   maximum-paths 32
   neighbor evpn-leaf-peers peer group
   neighbor evpn-leaf-peers next-hop-unchanged
   neighbor evpn-leaf-peers update-source Loopback0
   neighbor evpn-leaf-peers allowas-in 1
   neighbor evpn-leaf-peers ebgp-multihop 2
   neighbor evpn-leaf-peers timers 1 3
   neighbor evpn-leaf-peers send-community extended
   neighbor evpn-leaf-peers maximum-routes 0 warning-limit 90 percent
   neighbor underlay-export-all peer group
   neighbor underlay-export-all timers 1 3
   neighbor underlay-export-all route-map all-loopbacks in
   neighbor underlay-export-all route-map all-loopbacks out
   neighbor underlay-export-all maximum-routes 0 warning-limit 90 percent
   neighbor 10.0.0.32 peer group evpn-leaf-peers
   neighbor 10.0.0.32 remote-as 65100
   neighbor 10.0.0.32 description dc1-l1
   neighbor 10.10.0.32 peer group evpn-leaf-peers
   neighbor 10.10.0.32 remote-as 65200
   neighbor 10.10.0.32 description dc2-l1
   neighbor 10.20.0.32 peer group evpn-leaf-peers
   neighbor 10.20.0.32 remote-as 65300
   neighbor 10.20.0.32 description dc3-l1
   neighbor 10.100.0.1 peer group underlay-export-all
   neighbor 10.100.0.1 remote-as 65100
   neighbor 10.100.0.1 description dc1-l1
   neighbor 10.100.0.3 peer group underlay-export-all
   neighbor 10.100.0.3 remote-as 65200
   neighbor 10.100.0.3 description dc2-l1
   neighbor 10.100.0.5 peer group underlay-export-all
   neighbor 10.100.0.5 remote-as 65300
   neighbor 10.100.0.5 description dc3-l1
   redistribute connected route-map all-loopbacks
   !
   address-family evpn
      neighbor evpn-leaf-peers activate
   !
   address-family ipv4
      neighbor underlay-export-all activate
!
end

```


