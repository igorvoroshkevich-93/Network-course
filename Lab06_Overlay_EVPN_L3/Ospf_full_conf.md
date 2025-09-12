**Spine-1**

```
hostname Spine-1
!
interface Ethernet1
   no switchport
   ip address 10.2.0.0/31
   bfd interval 100 min-rx 100 multiplier 3
   ip ospf neighbor bfd
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.1
   ip ospf message-digest-key 10 md5 7 f3D/O8/wE3INainqlSQGGw==
!
interface Ethernet2
   no switchport
   ip address 10.2.0.2/31
   bfd interval 100 min-rx 100 multiplier 3
   ip ospf neighbor bfd
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.1
   ip ospf message-digest-key 10 md5 7 DYWREOc3d9jg+rXRFCgSAg==
!
interface Ethernet3
   no switchport
   ip address 10.2.0.4/31
   bfd interval 100 min-rx 100 multiplier 3
   ip ospf neighbor bfd
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.1
   ip ospf message-digest-key 10 md5 7 DYWREOc3d9jg+rXRFCgSAg==
!

interface Loopback0
   ip address 10.0.0.0/32
   ip ospf area 0.0.0.1
!
interface Loopback1
   ip address 10.1.0.0/32
   ip ospf area 0.0.0.1
!
ip routing
!
router ospf 1
   router-id 10.0.0.0
   max-lsa 12000
   passive-interface default
   no passive-interface ethernet 1
   no passive-interface ethernet 2
   no passive-interface ethernet 3
!

```


**Spine-2**

```
hostname Spine-2
!
interface Ethernet1
   no switchport
   ip address 10.2.1.0/31
   bfd interval 100 min-rx 100 multiplier 3
   ip ospf neighbor bfd
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.1
   ip ospf message-digest-key 10 md5 7 f3D/O8/wE3INainqlSQGGw==
!
interface Ethernet2
   no switchport
   ip address 10.2.1.2/31
   bfd interval 100 min-rx 100 multiplier 3
   ip ospf neighbor bfd
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.1
   ip ospf message-digest-key 10 md5 7 DYWREOc3d9jg+rXRFCgSAg==
!
interface Ethernet3
   no switchport
   ip address 10.2.1.4/31
   bfd interval 100 min-rx 100 multiplier 3
   ip ospf neighbor bfd
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.1
   ip ospf message-digest-key 10 md5 7 DYWREOc3d9jg+rXRFCgSAg==
!
interface Loopback0
   ip address 10.0.0.1/32
   ip ospf area 0.0.0.1
!
interface Loopback1
   ip address 10.1.0.1/32
   ip ospf area 0.0.0.1
!
ip routing
!
router ospf 1
   router-id 10.0.0.1
   max-lsa 12000
   passive-interface default
   no passive-interface ethernet 1
   no passive-interface ethernet 2
   no passive-interface ethernet 3
!

```


**Leaf-1**

```
hostname Leaf-1
!
interface Ethernet1
   no switchport
   ip address 10.2.0.1/31
   bfd interval 100 min-rx 100 multiplier 3
   ip ospf neighbor bfd
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.1
   ip ospf message-digest-key 10 md5 7 f3D/O8/wE3INainqlSQGGw==
!
interface Ethernet2
   no switchport
   ip address 10.2.1.1/31
   bfd interval 100 min-rx 100 multiplier 3
   ip ospf neighbor bfd
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.1
   ip ospf message-digest-key 10 md5 7 DYWREOc3d9jg+rXRFCgSAg==
!
interface Loopback0
   ip address 10.0.0.32/32
   ip ospf area 0.0.0.1
!
interface Loopback1
   ip address 10.1.0.32/32
   ip ospf area 0.0.0.1
!
ip routing
!
router ospf 1
   router-id 10.0.0.32
   max-lsa 12000
   passive-interface default
   no passive-interface ethernet 1
   no passive-interface ethernet 2
!

```

**Leaf-2**

```
hostname Leaf-2
!
interface Ethernet1
   no switchport
   ip address 10.2.0.3/31
   bfd interval 100 min-rx 100 multiplier 3
   ip ospf neighbor bfd
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.1
   ip ospf message-digest-key 10 md5 7 f3D/O8/wE3INainqlSQGGw==
!
interface Ethernet2
   no switchport
   ip address 10.2.1.3/31
   bfd interval 100 min-rx 100 multiplier 3
   ip ospf neighbor bfd
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.1
   ip ospf message-digest-key 10 md5 7 DYWREOc3d9jg+rXRFCgSAg==
!
interface Loopback0
   ip address 10.0.0.33/32
   ip ospf area 0.0.0.1
!
interface Loopback1
   ip address 10.1.0.33/32
   ip ospf area 0.0.0.1
!
ip routing
!
router ospf 1
   router-id 10.0.0.33
   max-lsa 12000
   passive-interface default
   no passive-interface ethernet 1
   no passive-interface ethernet 2
!

```

**Leaf-3**

```
hostname Leaf-3
!
interface Ethernet1
   no switchport
   ip address 10.2.0.5/31
   bfd interval 100 min-rx 100 multiplier 3
   ip ospf neighbor bfd
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.1
   ip ospf message-digest-key 10 md5 7 f3D/O8/wE3INainqlSQGGw==
!
interface Ethernet2
   no switchport
   ip address 10.2.1.5/31
   bfd interval 100 min-rx 100 multiplier 3
   ip ospf neighbor bfd
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.1
   ip ospf message-digest-key 10 md5 7 DYWREOc3d9jg+rXRFCgSAg==
!
interface Loopback0
   ip address 10.0.0.34/32
   ip ospf area 0.0.0.1
!
interface Loopback1
   ip address 10.1.0.34/32
   ip ospf area 0.0.0.1
!
ip routing
!
router ospf 1
   router-id 10.0.0.34
   max-lsa 12000
   passive-interface default
   no passive-interface ethernet 1
   no passive-interface ethernet 2
!

```
