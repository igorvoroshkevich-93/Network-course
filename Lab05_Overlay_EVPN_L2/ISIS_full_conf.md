**Spine-1**

```
hostname Spine-1
!
interface Ethernet1
   no switchport
   ip address 10.2.0.0/31
   bfd interval 100 min-rx 100 multiplier 3
   ipv6 address e342:4d31:8282:b010:2::/127
   isis enable OtusLab
   isis bfd
   isis circuit-type level-1
   isis network point-to-point
   isis authentication mode md5
   isis authentication key 7 sdBdkY0GZS6q6LgizvmSCw==
!
interface Ethernet2
   no switchport
   ip address 10.2.0.2/31
   bfd interval 100 min-rx 100 multiplier 3
   ipv6 address e342:4d31:8282:b010:2::2/127
   isis enable OtusLab
   isis bfd
   isis circuit-type level-1
   isis network point-to-point
   isis authentication mode md5
   isis authentication key 7 sdBdkY0GZS6q6LgizvmSCw==
!
interface Ethernet3
   no switchport
   ip address 10.2.0.4/31
   bfd interval 100 min-rx 100 multiplier 3
   ipv6 address e342:4d31:8282:b010:2::4/127
   isis enable OtusLab
   isis bfd
   isis circuit-type level-1
   isis network point-to-point
   isis authentication mode md5
   isis authentication key 7 sdBdkY0GZS6q6LgizvmSCw==
!
interface Ethernet4
   isis enable OtusLab
   isis passive
!
interface Ethernet5
   isis enable OtusLab
   isis passive
!
interface Ethernet6
   isis enable OtusLab
   isis passive
!
interface Ethernet7
   isis enable OtusLab
   isis passive
!
interface Ethernet8
   isis enable OtusLab
   isis passive
!
interface Loopback0
   ip address 10.0.0.0/32
   ipv6 address e342:4d31:8282:b010::/128
   isis enable OtusLab
!
interface Loopback1
   ip address 10.1.0.0/32
   ipv6 address e342:4d31:8282:b010:1::/128
   isis enable OtusLab
!
ip routing
!
ipv6 unicast-routing
!
router isis OtusLab
   net 49.0001.0001.0001.1000.00
   !
   address-family ipv4 unicast
   !
   address-family ipv6 unicast
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
   isis enable OtusLab
   isis bfd
   isis circuit-type level-1
   isis network point-to-point
   isis authentication mode md5
   isis authentication key 7 sdBdkY0GZS6q6LgizvmSCw==
!
interface Ethernet2
   no switchport
   ip address 10.2.1.2/31
   bfd interval 100 min-rx 100 multiplier 3
   ipv6 address e342:4d31:8282:b010:2:1:0:2/127
   isis enable OtusLab
   isis bfd
   isis circuit-type level-1
   isis network point-to-point
   isis authentication mode md5
   isis authentication key 7 sdBdkY0GZS6q6LgizvmSCw==
!
interface Ethernet3
   no switchport
   ip address 10.2.1.4/31
   bfd interval 100 min-rx 100 multiplier 3
   ipv6 address e342:4d31:8282:b010:2:1:0:4/127
   isis enable OtusLab
   isis bfd
   isis circuit-type level-1
   isis network point-to-point
   isis authentication mode md5
   isis authentication key 7 sdBdkY0GZS6q6LgizvmSCw==
!
interface Ethernet4
   isis enable OtusLab
   isis passive
!
interface Ethernet5
   isis enable OtusLab
   isis passive
!
interface Ethernet6
   isis enable OtusLab
   isis passive
!
interface Ethernet7
   isis enable OtusLab
   isis passive
!
interface Ethernet8
   isis enable OtusLab
   isis passive
!
interface Loopback0
   ip address 10.0.0.1/32
   ipv6 address e342:4d31:8282:b010::1/128
   isis enable OtusLab
!
interface Loopback1
   ip address 10.1.0.1/32
   ipv6 address e342:4d31:8282:b010:1::1/128
   isis enable OtusLab
!
ip routing
!
ipv6 unicast-routing
!
router isis OtusLab
   net 49.0001.0001.0001.1001.00
   !
   address-family ipv4 unicast
   !
   address-family ipv6 unicast
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
   isis enable OtusLab
   isis bfd
   isis circuit-type level-1
   isis network point-to-point
   isis authentication mode md5
   isis authentication key 7 sdBdkY0GZS6q6LgizvmSCw==
!
interface Ethernet2
   no switchport
   ip address 10.2.1.1/31
   bfd interval 100 min-rx 100 multiplier 3
   ipv6 address e342:4d31:8282:b010:2:1:0:1/127
   isis enable OtusLab
   isis bfd
   isis circuit-type level-1
   isis network point-to-point
   isis authentication mode md5
   isis authentication key 7 sdBdkY0GZS6q6LgizvmSCw==
!
interface Ethernet3
!
interface Ethernet4
   isis enable OtusLab
   isis passive
!
interface Ethernet5
   isis enable OtusLab
   isis passive
!
interface Ethernet6
   isis enable OtusLab
   isis passive
!
interface Ethernet7
   isis enable OtusLab
   isis passive
!
interface Ethernet8
   isis enable OtusLab
   isis passive
!
interface Loopback0
   ip address 10.0.0.32/32
   ipv6 address e342:4d31:8282:b010::10/128
   isis enable OtusLab
!
interface Loopback1
   ip address 10.1.0.32/32
   ipv6 address e342:4d31:8282:b010:1::10/128
   isis enable OtusLab
!
ip routing
!
ipv6 unicast-routing
!
router isis OtusLab
   net 49.0001.0001.0001.2000.00
   !
   address-family ipv4 unicast
   !
   address-family ipv6 unicast
!
end

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
   isis enable OtusLab
   isis bfd
   isis circuit-type level-1
   isis network point-to-point
   isis authentication mode md5
   isis authentication key 7 sdBdkY0GZS6q6LgizvmSCw==
!
interface Ethernet2
   no switchport
   ip address 10.2.1.3/31
   bfd interval 100 min-rx 100 multiplier 3
   ipv6 address e342:4d31:8282:b010:2:1:0:3/127
   isis enable OtusLab
   isis bfd
   isis circuit-type level-1
   isis network point-to-point
   isis authentication mode md5
   isis authentication key 7 sdBdkY0GZS6q6LgizvmSCw==
!
interface Ethernet3
!
interface Ethernet4
   isis enable OtusLab
   isis passive
!
interface Ethernet5
   isis enable OtusLab
   isis passive
!
interface Ethernet6
   isis enable OtusLab
   isis passive
!
interface Ethernet7
   isis enable OtusLab
   isis passive
!
interface Ethernet8
   isis enable OtusLab
   isis passive
!
interface Loopback0
   ip address 10.0.0.33/32
   ipv6 address e342:4d31:8282:b010::11/128
   isis enable OtusLab
!
interface Loopback1
   ip address 10.1.0.33/32
   ipv6 address e342:4d31:8282:b010:1::11/128
   isis enable OtusLab
!
ip routing
!
ipv6 unicast-routing
!
router isis OtusLab
   net 49.0001.0001.0001.2001.00
   !
   address-family ipv4 unicast
   !
   address-family ipv6 unicast
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
   isis enable OtusLab
   isis bfd
   isis circuit-type level-1
   isis network point-to-point
   isis authentication mode md5
   isis authentication key 7 sdBdkY0GZS6q6LgizvmSCw==
!
interface Ethernet2
   no switchport
   ip address 10.2.1.5/31
   bfd interval 100 min-rx 100 multiplier 3
   ipv6 address e342:4d31:8282:b010:2:1:0:5/127
   isis enable OtusLab
   isis bfd
   isis circuit-type level-1
   isis network point-to-point
   isis authentication mode md5
   isis authentication key 7 sdBdkY0GZS6q6LgizvmSCw==
!
interface Ethernet3
!
interface Ethernet4
   isis enable OtusLab
   isis passive
!
interface Ethernet5
   isis enable OtusLab
   isis passive
!
interface Ethernet6
   isis enable OtusLab
   isis passive
!
interface Ethernet7
   isis enable OtusLab
   isis passive
!
interface Ethernet8
   isis enable OtusLab
   isis passive
!
interface Loopback0
   ip address 10.0.0.34/32
   ipv6 address e342:4d31:8282:b010::12/128
   isis enable OtusLab
!
interface Loopback1
   ip address 10.1.0.34/32
   ipv6 address e342:4d31:8282:b010:1::12/128
   isis enable OtusLab
!
ip routing
!
ipv6 unicast-routing
!
router isis OtusLab
   net 49.0001.0001.0001.2002.00
   !
   address-family ipv4 unicast
   !
   address-family ipv6 unicast
!
end

```
