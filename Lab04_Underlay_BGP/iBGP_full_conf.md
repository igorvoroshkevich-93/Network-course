**Spine-1**

```
hostname Spine-1
!
interface Ethernet1
   no switchport
   ip address 10.2.0.0/31
   bfd interval 100 min-rx 100 multiplier 3
   ipv6 address e342:4d31:8282:b010:2::/127
!
interface Ethernet2
   no switchport
   ip address 10.2.0.2/31
   bfd interval 100 min-rx 100 multiplier 3
   ipv6 address e342:4d31:8282:b010:2::2/127
!
interface Ethernet3
   no switchport
   ip address 10.2.0.4/31
   bfd interval 100 min-rx 100 multiplier 3
   ipv6 address e342:4d31:8282:b010:2::4/127
!
interface Loopback0
   ip address 10.0.0.0/32
   ipv6 address e342:4d31:8282:b010::/128
!
interface Loopback1
   ip address 10.1.0.0/32
   ipv6 address e342:4d31:8282:b010:1::/128
!
ip routing
!
ip prefix-list Spine_LB seq 10 permit 10.0.0.0/32
ip prefix-list Spine_LB seq 20 permit 10.1.0.0/32
ip prefix-list Spine_LB seq 30 permit 10.0.0.32/32
ip prefix-list Spine_LB seq 40 permit 10.0.0.33/32
ip prefix-list Spine_LB seq 50 permit 10.0.0.34/32
ip prefix-list Spine_LB seq 60 permit 10.1.0.32/32
ip prefix-list Spine_LB seq 70 permit 10.1.0.33/32
ip prefix-list Spine_LB seq 80 permit 10.1.0.34/32
!
ipv6 prefix-list Spine_v6LB
   seq 10 permit e342:4d31:8282:b010::/128
   seq 20 permit e342:4d31:8282:b010:1::/128
   seq 30 permit e342:4d31:8282:b010::10/128
   seq 40 permit e342:4d31:8282:b010::11/128
   seq 50 permit e342:4d31:8282:b010::12/128
   seq 60 permit e342:4d31:8282:b010:1::10/128
   seq 70 permit e342:4d31:8282:b010:1::11/128
   seq 80 permit e342:4d31:8282:b010:1::12/128
!
ipv6 unicast-routing
!
route-map Loopback permit 10
   match ipv6 address prefix-list Spine_v6LB
!
route-map Loopback permit 20
   match ip address prefix-list Spine_LB
!
router bgp 65100
   router-id 10.0.0.0
   maximum-paths 2
   neighbor iBGP_Underlay peer group
   neighbor iBGP_Underlay remote-as 65100
   neighbor iBGP_Underlay next-hop-self
   neighbor iBGP_Underlay bfd
   neighbor iBGP_Underlay route-reflector-client
   neighbor iBGP_Underlay timers 3 9
   neighbor iBGP_Underlay route-map Loopback out
   neighbor iBGP_Underlay password 7 RciLB8NStXJELSRcl0MJ8Q1r7+TVt0Qd
   neighbor 10.2.0.1 peer group iBGP_Underlay
   neighbor 10.2.0.3 peer group iBGP_Underlay
   neighbor 10.2.0.5 peer group iBGP_Underlay
   neighbor e342:4d31:8282:b010:2::1 peer group iBGP_Underlay
   neighbor e342:4d31:8282:b010:2::3 peer group iBGP_Underlay
   neighbor e342:4d31:8282:b010:2::5 peer group iBGP_Underlay
   redistribute connected route-map Loopback
   !
   address-family ipv4
      neighbor 10.2.0.1 activate
      neighbor 10.2.0.3 activate
      neighbor 10.2.0.5 activate
   !
   address-family ipv6
      neighbor e342:4d31:8282:b010:2::1 activate
      neighbor e342:4d31:8282:b010:2::3 activate
      neighbor e342:4d31:8282:b010:2::5 activate
!
end
```

**Spine-2**

```
hostname Spine-2
!
interface Ethernet1
   no switchport
   ip address 10.2.1.0/31
   bfd interval 100 min-rx 100 multiplier 3
   ipv6 address e342:4d31:8282:b010:2:1::/127
!
interface Ethernet2
   no switchport
   ip address 10.2.1.2/31
   bfd interval 100 min-rx 100 multiplier 3
   ipv6 address e342:4d31:8282:b010:2:1:0:2/127
!
interface Ethernet3
   no switchport
   ip address 10.2.1.4/31
   bfd interval 100 min-rx 100 multiplier 3
   ipv6 address e342:4d31:8282:b010:2:1:0:4/127
!
interface Loopback0
   ip address 10.0.0.1/32
   ipv6 address e342:4d31:8282:b010::1/128
!
interface Loopback1
   ip address 10.1.0.1/32
   ipv6 address e342:4d31:8282:b010:1::1/128
!
ip routing
!
ip prefix-list Spine_LB seq 10 permit 10.0.0.1/32
ip prefix-list Spine_LB seq 20 permit 10.1.0.1/32
ip prefix-list Spine_LB seq 30 permit 10.0.0.32/32
ip prefix-list Spine_LB seq 40 permit 10.0.0.33/32
ip prefix-list Spine_LB seq 50 permit 10.0.0.34/32
ip prefix-list Spine_LB seq 60 permit 10.1.0.32/32
ip prefix-list Spine_LB seq 70 permit 10.1.0.33/32
ip prefix-list Spine_LB seq 80 permit 10.1.0.34/32
!
ipv6 prefix-list Spine_v6LB
   seq 10 permit e342:4d31:8282:b010::1/128
   seq 20 permit e342:4d31:8282:b010:1::1/128
   seq 30 permit e342:4d31:8282:b010::10/128
   seq 40 permit e342:4d31:8282:b010::11/128
   seq 50 permit e342:4d31:8282:b010::12/128
   seq 60 permit e342:4d31:8282:b010:1::12/128
   seq 70 permit e342:4d31:8282:b010:1::11/128
   seq 80 permit e342:4d31:8282:b010:1::10/128
!
ipv6 unicast-routing
!
route-map Loopback permit 10
   match ipv6 address prefix-list Spine_v6LB
!
route-map Loopback permit 20
   match ip address prefix-list Spine_LB
!
router bgp 65100
   router-id 10.0.0.1
   maximum-paths 2
   neighbor iBGP_Underlay peer group
   neighbor iBGP_Underlay remote-as 65100
   neighbor iBGP_Underlay next-hop-self
   neighbor iBGP_Underlay bfd
   neighbor iBGP_Underlay route-reflector-client
   neighbor iBGP_Underlay timers 3 9
   neighbor iBGP_Underlay route-map Loopback out
   neighbor iBGP_Underlay password 7 RciLB8NStXJELSRcl0MJ8Q1r7+TVt0Qd
   neighbor 10.2.1.1 peer group iBGP_Underlay
   neighbor 10.2.1.3 peer group iBGP_Underlay
   neighbor 10.2.1.5 peer group iBGP_Underlay
   neighbor e342:4d31:8282:b010:2:1:0:1 peer group iBGP_Underlay
   neighbor e342:4d31:8282:b010:2:1:0:3 peer group iBGP_Underlay
   neighbor e342:4d31:8282:b010:2:1:0:5 peer group iBGP_Underlay
   redistribute connected route-map Loopback

   !
   address-family ipv4
      neighbor 10.2.1.1 activate
      neighbor 10.2.1.3 activate
      neighbor 10.2.1.5 activate
   !
   address-family ipv6
      neighbor e342:4d31:8282:b010:2:1:0:1 activate
      neighbor e342:4d31:8282:b010:2:1:0:3 activate
      neighbor e342:4d31:8282:b010:2:1:0:5 activate
!
end
```



**Leaf-1**

```
hostname Leaf-1
!
interface Ethernet1
   no switchport
   ip address 10.2.0.1/31
   bfd interval 100 min-rx 100 multiplier 3
   ipv6 address e342:4d31:8282:b010:2::1/127
!
interface Ethernet2
   no switchport
   ip address 10.2.1.1/31
   bfd interval 100 min-rx 100 multiplier 3
   ipv6 address e342:4d31:8282:b010:2:1:0:1/127
!
interface Loopback0
   ip address 10.0.0.32/32
   ipv6 address e342:4d31:8282:b010::10/128
!
interface Loopback1
   ip address 10.1.0.32/32
   ipv6 address e342:4d31:8282:b010:1::10/128
!
ip routing
!
ipv6 unicast-routing
!
router bgp 65100
   router-id 10.0.0.32
   maximum-paths 2
   neighbor iBGP_Underlay peer group
   neighbor iBGP_Underlay remote-as 65100
   neighbor iBGP_Underlay next-hop-self
   neighbor iBGP_Underlay bfd
   neighbor iBGP_Underlay timers 3 9
   neighbor iBGP_Underlay password 7 RciLB8NStXJELSRcl0MJ8Q1r7+TVt0Qd
   neighbor 10.2.0.0 peer group iBGP_Underlay
   neighbor 10.2.1.0 peer group iBGP_Underlay
   neighbor e342:4d31:8282:b010:2:: peer group iBGP_Underlay
   neighbor e342:4d31:8282:b010:2:1:: peer group iBGP_Underlay
   !
   address-family ipv4
      neighbor 10.2.0.0 activate
      neighbor 10.2.1.0 activate
      network 10.0.0.32/32
      network 10.1.0.32/32
   !
   address-family ipv6
      neighbor e342:4d31:8282:b010:2:: activate
      neighbor e342:4d31:8282:b010:2:1:: activate
      network e342:4d31:8282:b010::10/128
      network e342:4d31:8282:b010:1::10/128

```

**Leaf-2**

```
hostname Leaf-2
!
interface Ethernet1
   no switchport
   ip address 10.2.0.3/31
   bfd interval 100 min-rx 100 multiplier 3
   ipv6 address e342:4d31:8282:b010:2::3/127
!
interface Ethernet2
   no switchport
   ip address 10.2.1.3/31
   bfd interval 100 min-rx 100 multiplier 3
   ipv6 address e342:4d31:8282:b010:2:1:0:3/127
!
interface Loopback0
   ip address 10.0.0.33/32
   ipv6 address e342:4d31:8282:b010::11/128
!
interface Loopback1
   ip address 10.1.0.33/32
   ipv6 address e342:4d31:8282:b010:1::11/128
!
ip routing
!
ipv6 unicast-routing
!
router bgp 65100
   router-id 10.0.0.33
   maximum-paths 2
   neighbor iBGP_Underlay peer group
   neighbor iBGP_Underlay remote-as 65100
   neighbor iBGP_Underlay next-hop-self
   neighbor iBGP_Underlay bfd
   neighbor iBGP_Underlay timers 3 9
   neighbor iBGP_Underlay password 7 RciLB8NStXJELSRcl0MJ8Q1r7+TVt0Qd
   neighbor 10.2.0.2 peer group iBGP_Underlay
   neighbor 10.2.1.2 peer group iBGP_Underlay
   neighbor e342:4d31:8282:b010:2::2 peer group iBGP_Underlay
   neighbor e342:4d31:8282:b010:2:1:0:2 peer group iBGP_Underlay
   !
   address-family ipv4
      neighbor 10.2.0.2 activate
      neighbor 10.2.1.2 activate
      network 10.0.0.33/32
      network 10.1.0.33/32
   !
   address-family ipv6
      neighbor e342:4d31:8282:b010:2::2 activate
      neighbor e342:4d31:8282:b010:2:1:0:2 activate
      network e342:4d31:8282:b010::11/128
      network e342:4d31:8282:b010:1::11/128
!
end

```

**Leaf-3**

```
hostname Leaf-3
!
interface Ethernet1
   no switchport
   ip address 10.2.0.5/31
   bfd interval 100 min-rx 100 multiplier 3
   ipv6 address e342:4d31:8282:b010:2::5/127
!
interface Ethernet2
   no switchport
   ip address 10.2.1.5/31
   bfd interval 100 min-rx 100 multiplier 3
   ipv6 address e342:4d31:8282:b010:2:1:0:5/127
!
interface Loopback0
   ip address 10.0.0.34/32
   ipv6 address e342:4d31:8282:b010::12/128
!
interface Loopback1
   ip address 10.1.0.34/32
   ipv6 address e342:4d31:8282:b010:1::12/128
!
ip routing
!
ipv6 unicast-routing
!
router bgp 65100
   router-id 10.0.0.34
   maximum-paths 2
   neighbor iBGP_Underlay peer group
   neighbor iBGP_Underlay remote-as 65100
   neighbor iBGP_Underlay next-hop-self
   neighbor iBGP_Underlay bfd
   neighbor iBGP_Underlay timers 3 9
   neighbor iBGP_Underlay password 7 RciLB8NStXJELSRcl0MJ8Q1r7+TVt0Qd
   neighbor 10.2.0.4 peer group iBGP_Underlay
   neighbor 10.2.1.4 peer group iBGP_Underlay
   neighbor e342:4d31:8282:b010:2::4 peer group iBGP_Underlay
   neighbor e342:4d31:8282:b010:2:1:0:4 peer group iBGP_Underlay
   !
   address-family ipv4
      neighbor 10.2.0.4 activate
      neighbor 10.2.1.4 activate
      network 10.0.0.34/32
      network 10.1.0.34/32
   !
   address-family ipv6
      neighbor e342:4d31:8282:b010:2::4 activate
      neighbor e342:4d31:8282:b010:2:1:0:4 activate
      network e342:4d31:8282:b010::12/128
      network e342:4d31:8282:b010:1::12/128
!
end

```