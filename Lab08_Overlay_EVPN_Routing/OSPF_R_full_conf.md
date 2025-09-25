
**Spine-1**

```
hostname Spine-1
!
interface Ethernet1
   no switchport
   ip address 10.2.0.0/31
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.1
   ip ospf message-digest-key 10 md5 0 Otus_Mlag
!
interface Ethernet2
   no switchport
   ip address 10.2.0.2/31
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.1
   ip ospf message-digest-key 10 md5 0 Otus_Mlag
!
interface Ethernet3
   no switchport
   ip address 10.2.0.4/31
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.1
   ip ospf message-digest-key 10 md5 0 Otus_Mlag
!
interface Ethernet4
   no switchport
   ip address 10.2.0.6/31
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.1
   ip ospf message-digest-key 10 md5 0 Otus_Mlag
!
interface Ethernet5
   no switchport
   ip address 10.2.0.8/31
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.1
   ip ospf message-digest-key 10 md5 0 Otus_Mlag
!
interface Ethernet6
   no switchport
   ip address 10.2.0.10/31
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.1
   ip ospf message-digest-key 10 md5 0 Otus_Mlag
!

interface Loopback0
   ip address 10.0.0.0/32
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
   no passive-interface ethernet 4
   no passive-interface ethernet 5
   no passive-interface ethernet 6
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
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.1
   ip ospf message-digest-key 10 md5 0 Otus_Mlag
!
interface Ethernet2
   no switchport
   ip address 10.2.1.2/31
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.1
   ip ospf message-digest-key 10 md5 0 Otus_Mlag
!
interface Ethernet3
   no switchport
   ip address 10.2.1.4/31
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.1
   ip ospf message-digest-key 10 md5 0 Otus_Mlag
!
interface Ethernet4
   no switchport
   ip address 10.2.1.6/31
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.1
   ip ospf message-digest-key 10 md5 0 Otus_Mlag
!
interface Ethernet5
   no switchport
   ip address 10.2.1.8/31
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.1
   ip ospf message-digest-key 10 md5 0 Otus_Mlag
!
interface Ethernet6
   no switchport
   ip address 10.2.1.10/31
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.1
   ip ospf message-digest-key 10 md5 0 Otus_Mlag
!
interface Loopback0
   ip address 10.0.0.1/32
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
   no passive-interface ethernet 4
   no passive-interface ethernet 5
   no passive-interface ethernet 6
!
end

```


**Leaf-1**

```
!
interface Ethernet1
   no switchport
   ip address 10.2.0.1/31
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.1
   ip ospf message-digest-key 10 md5 0 Otus_Mlag
!
interface Ethernet2
   no switchport
   ip address 10.2.1.1/31
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.1
   ip ospf message-digest-key 10 md5 0 Otus_Mlag
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
   no passive-interface port-Channel 10
!
end

```

**Leaf-2**

```
!
interface Ethernet1
   no switchport
   ip address 10.2.0.3/31
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.1
   ip ospf message-digest-key 10 md5 0 Otus_Mlag
!
interface Ethernet2
   no switchport
   ip address 10.2.1.3/31
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.1
   ip ospf message-digest-key 10 md5 0 Otus_Mlag
!
interface Loopback0
   ip address 10.0.0.33/32
   ip ospf area 0.0.0.1
!
interface Loopback1
   ip address 10.1.0.32/32
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
   no passive-interface port-Channel 10
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
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.1
   ip ospf message-digest-key 10 md5 0 Otus_Mlag
!
interface Ethernet2
   no switchport
   ip address 10.2.1.5/31
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.1
   ip ospf message-digest-key 10 md5 0 Otus_Mlag
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
end

```

**Leaf-4**

```
hostname Leaf-4
!
interface Ethernet1
   no switchport
   ip address 10.2.0.7/31
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.1
   ip ospf message-digest-key 10 md5 0 Otus_Mlag
!
interface Ethernet2
   no switchport
   ip address 10.2.1.7/31
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.1
   ip ospf message-digest-key 10 md5 0 Otus_Mlag
!
interface Loopback0
   ip address 10.0.0.35/32
   ip ospf area 0.0.0.1
!
interface Loopback1
   ip address 10.1.0.34/32
   ip ospf area 0.0.0.1
!
ip routing
!
router ospf 1
   router-id 10.0.0.35
   max-lsa 12000
   passive-interface default
   no passive-interface ethernet 1
   no passive-interface ethernet 2
!
end

```



**Leaf-5**

```
!
interface Ethernet1
   no switchport
   ip address 10.2.0.9/31
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.1
   ip ospf message-digest-key 10 md5 0 Otus_Mlag
!
interface Ethernet2
   no switchport
   ip address 10.2.1.9/31
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.1
   ip ospf message-digest-key 10 md5 0 Otus_Mlag
!
interface Loopback0
   ip address 10.0.0.36/32
   ip ospf area 0.0.0.1
!
interface Loopback1
   ip address 10.1.0.36/32
   ip ospf area 0.0.0.1
!
ip routing
!
router ospf 1
   router-id 10.0.0.36
   max-lsa 12000
   passive-interface default
   no passive-interface ethernet 1
   no passive-interface ethernet 2
   no passive-interface port-Channel 10
!
end

```

**Leaf-6**

```
!
interface Ethernet1
   no switchport
   ip address 10.2.0.11/31
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.1
   ip ospf message-digest-key 10 md5 0 Otus_Mlag
!
interface Ethernet2
   no switchport
   ip address 10.2.1.11/31
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf area 0.0.0.1
   ip ospf message-digest-key 10 md5 0 Otus_Mlag
!
interface Loopback0
   ip address 10.0.0.37/32
   ip ospf area 0.0.0.1
!
interface Loopback1
   ip address 10.1.0.36/32
   ip ospf area 0.0.0.1
!
ip routing
!
router ospf 1
   router-id 10.0.0.37
   max-lsa 12000
   passive-interface default
   no passive-interface ethernet 1
   no passive-interface ethernet 2
   no passive-interface port-Channel 10
!
end

```
