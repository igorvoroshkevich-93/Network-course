**Spine-1**

```
hostname Spine-1
!
spanning-tree mode mstp
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
   ip address 10.0.0.0/32
   ipv6 address e342:4d31:8282:b010::/128
!
interface Loopback1
   ip address 10.1.0.0/32
   ipv6 address e342:4d31:8282:b010:1::/128
!
interface Management1
!
ip routing
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
ipv6 prefix-list Leaf_v6LB
   seq 10 permit e342:4d31:8282:b010::10/128
   seq 20 permit e342:4d31:8282:b010::11/128
   seq 30 permit e342:4d31:8282:b010::12/128
   seq 40 permit e342:4d31:8282:b010:1::10/128
   seq 50 permit e342:4d31:8282:b010:1::11/128
   seq 60 permit e342:4d31:8282:b010:1::12/128
!
ipv6 prefix-list Spine_v6LB
   seq 10 permit e342:4d31:8282:b010::/128
   seq 20 permit e342:4d31:8282:b010:1::/128
!
ipv6 unicast-routing
!
route-map Loopback permit 10
   match ip address prefix-list Spine_LB
!
route-map Loopback permit 20
   match ipv6 address prefix-list Spine_v6LB
!
route-map Loopback permit 30
   match ip address prefix-list Leaf_LB
!
route-map Loopback permit 40
   match ipv6 address prefix-list Leaf_v6LB
!
peer-filter ASN_Leafs
   10 match as-range 65101-65199 result accept
!
router bgp 65100
   router-id 10.0.0.0
   bgp listen range 10.2.0.0/29 peer-group eBGP_Underlay peer-filter ASN_Leafs
   neighbor eBGP_Underlay peer group
   neighbor eBGP_Underlay bfd
   neighbor eBGP_Underlay timers 3 9
   neighbor eBGP_Underlay route-map Loopback out
   neighbor eBGP_Underlay password 7 E48d813jH6Aa47FAmz4bQtZEJ+FblObH
   neighbor e342:4d31:8282:b010:2::1 peer group eBGP_Underlay
   neighbor e342:4d31:8282:b010:2::1 remote-as 65101
   neighbor e342:4d31:8282:b010:2::3 peer group eBGP_Underlay
   neighbor e342:4d31:8282:b010:2::3 remote-as 65102
   neighbor e342:4d31:8282:b010:2::5 peer group eBGP_Underlay
   neighbor e342:4d31:8282:b010:2::5 remote-as 65103
   redistribute connected route-map Loopback_Spine
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
spanning-tree mode mstp
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
   ip address 10.0.0.1/32
   ipv6 address e342:4d31:8282:b010::1/128
!
interface Loopback1
   ip address 10.1.0.1/32
   ipv6 address e342:4d31:8282:b010:1::1/128
!
interface Management1
!
ip routing
!
ip prefix-list Leaf_LB seq 10 permit 10.0.0.32/32
ip prefix-list Leaf_LB seq 20 permit 10.0.0.33/32
ip prefix-list Leaf_LB seq 30 permit 10.0.0.34/32
ip prefix-list Leaf_LB seq 40 permit 10.1.0.32/32
ip prefix-list Leaf_LB seq 50 permit 10.1.0.33/32
ip prefix-list Leaf_LB seq 60 permit 10.1.0.34/32
ip prefix-list Spine_LB seq 10 permit 10.0.0.1/32
ip prefix-list Spine_LB seq 20 permit 10.1.0.1/32
!
ipv6 prefix-list Leaf_v6LB
   seq 10 permit e342:4d31:8282:b010::10/128
   seq 20 permit e342:4d31:8282:b010::11/128
   seq 30 permit e342:4d31:8282:b010::12/128
   seq 40 permit e342:4d31:8282:b010:1::10/128
   seq 50 permit e342:4d31:8282:b010:1::11/128
   seq 60 permit e342:4d31:8282:b010:1::12/128
!
ipv6 prefix-list Spine_v6LB
   seq 10 permit e342:4d31:8282:b010::1/128
   seq 20 permit e342:4d31:8282:b010:1::1/128
!
ipv6 unicast-routing
!
route-map Loopback permit 10
   match ip address prefix-list Spine_LB
!
route-map Loopback permit 20
   match ipv6 address prefix-list Spine_v6LB
!
route-map Loopback permit 30
   match ip address prefix-list Leaf_LB
!
route-map Loopback permit 40
   match ip address prefix-list Leaf_v6LB
!
peer-filter ASN_Leafs
   10 match as-range 65101-65199 result accept
!
router bgp 65100
   router-id 10.0.0.1
   bgp listen range 10.2.1.0/29 peer-group eBGP_Underlay peer-filter ASN_Leafs
   neighbor eBGP_Underlay peer group
   neighbor eBGP_Underlay bfd
   neighbor eBGP_Underlay timers 3 9
   neighbor eBGP_Underlay route-map Loopback out
   neighbor eBGP_Underlay password 7 E48d813jH6Aa47FAmz4bQtZEJ+FblObH
   neighbor e342:4d31:8282:b010:2:1:0:1 peer group eBGP_Underlay
   neighbor e342:4d31:8282:b010:2:1:0:1 remote-as 65101
   neighbor e342:4d31:8282:b010:2:1:0:3 peer group eBGP_Underlay
   neighbor e342:4d31:8282:b010:2:1:0:3 remote-as 65102
   neighbor e342:4d31:8282:b010:2:1:0:5 peer group eBGP_Underlay
   neighbor e342:4d31:8282:b010:2:1:0:5 remote-as 65103
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
spanning-tree mode mstp
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
interface Ethernet3
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
   ip address 10.0.0.32/32
   ipv6 address e342:4d31:8282:b010::10/128
!
interface Loopback1
   ip address 10.1.0.32/32
   ipv6 address e342:4d31:8282:b010:1::10/128
!
interface Management1
!
ip routing
!
ipv6 unicast-routing
!
router bgp 65101
   router-id 10.0.0.32
   maximum-paths 2
   neighbor eBGP_Underlay peer group
   neighbor eBGP_Underlay remote-as 65100
   neighbor eBGP_Underlay bfd
   neighbor eBGP_Underlay timers 3 9
   neighbor eBGP_Underlay password 7 E48d813jH6Aa47FAmz4bQtZEJ+FblObH
   neighbor 10.2.0.0 peer group eBGP_Underlay
   neighbor 10.2.1.0 peer group eBGP_Underlay
   neighbor e342:4d31:8282:b010:2:: peer group eBGP_Underlay
   neighbor e342:4d31:8282:b010:2:1:: peer group eBGP_Underlay
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
!
end

```

**Leaf-2**

```
hostname Leaf-2
!
spanning-tree mode mstp
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
interface Ethernet3
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
   ip address 10.0.0.33/32
   ipv6 address e342:4d31:8282:b010::11/128
!
interface Loopback1
   ip address 10.1.0.33/32
   ipv6 address e342:4d31:8282:b010:1::11/128
!
interface Management1
!
ip routing
!
ipv6 unicast-routing
!
router bgp 65102
   router-id 10.0.0.33
   maximum-paths 2
   neighbor eBGP_Underlay peer group
   neighbor eBGP_Underlay remote-as 65100
   neighbor eBGP_Underlay bfd
   neighbor eBGP_Underlay timers 3 9
   neighbor eBGP_Underlay password 7 E48d813jH6Aa47FAmz4bQtZEJ+FblObH
   neighbor 10.2.0.2 peer group eBGP_Underlay
   neighbor 10.2.1.2 peer group eBGP_Underlay
   neighbor e342:4d31:8282:b010:2::2 peer group eBGP_Underlay
   neighbor e342:4d31:8282:b010:2:1:0:2 peer group eBGP_Underlay
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
spanning-tree mode mstp
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
interface Ethernet3
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
   ip address 10.0.0.34/32
   ipv6 address e342:4d31:8282:b010::12/128
!
interface Loopback1
   ip address 10.1.0.34/32
   ipv6 address e342:4d31:8282:b010:1::12/128
!
interface Management1
!
ip routing
!
ipv6 unicast-routing
!
router bgp 65103
   router-id 10.0.0.34
   maximum-paths 2
   neighbor eBGP_Underlay peer group
   neighbor eBGP_Underlay remote-as 65100
   neighbor eBGP_Underlay bfd
   neighbor eBGP_Underlay timers 3 9
   neighbor eBGP_Underlay password 7 E48d813jH6Aa47FAmz4bQtZEJ+FblObH
   neighbor 10.2.0.4 peer group eBGP_Underlay
   neighbor 10.2.1.4 peer group eBGP_Underlay
   neighbor e342:4d31:8282:b010:2::4 peer group eBGP_Underlay
   neighbor e342:4d31:8282:b010:2:1:0:4 peer group eBGP_Underlay
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
