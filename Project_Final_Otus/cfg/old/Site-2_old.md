**Agg-1**

```
no aaa root
!
transceiver qsfp default-mode 4x10G
!
service routing protocols model multi-agent
!
hostname Spine-1-Site-2
!
spanning-tree mode mstp
!
vlan 1000
   name Vlan-1000-Client-1
!
vlan 1001
   name Vlan-1001-Client-2
!
vlan 1002
   name Vlan-1002-Client-3
!
vlan 1003
   name Vlan-1003-Client-4
!
interface Ethernet1
!
interface Ethernet2
!
interface Ethernet3
!
interface Ethernet4
!
interface Ethernet5
!
interface Ethernet6
   switchport trunk allowed vlan 1001
   switchport mode trunk
!
interface Ethernet7
   switchport trunk allowed vlan 1000-1003
   switchport mode trunk
!
interface Ethernet8
   switchport trunk allowed vlan 1000-1003
   switchport mode trunk
!
interface Management1
!
interface Vlan1001
   ip address 192.168.101.5/24
!
ip routing
!
end

```



**TOR-3**

```
no aaa root
!
transceiver qsfp default-mode 4x10G
!
service routing protocols model multi-agent
!
hostname Leaf-3-Site-2
!
spanning-tree mode mstp
!
vlan 1001
   name Vlan-1001-Client-2
!
interface Ethernet1
   shutdown
!
interface Ethernet2
   switchport trunk allowed vlan 1001
   switchport mode trunk
!
interface Ethernet3
!
interface Ethernet4
   switchport trunk allowed vlan 1001
   switchport mode trunk
!
interface Ethernet5
!
interface Ethernet6
!
interface Ethernet7
!
interface Ethernet8
!
interface Management1
!
interface Vlan1001
   ip address 192.168.101.3/24
!
ip routing
!
end

```


**Client-2**

```
no aaa root
!
transceiver qsfp default-mode 4x10G
!
service routing protocols model multi-agent
!
hostname Client-2
!
spanning-tree mode mstp
!
vlan 1001
   name Client-1_OLD
!
interface Ethernet1
   shutdown
!
interface Ethernet2
   shutdown
!
interface Ethernet3
   switchport trunk allowed vlan 1001
   switchport mode trunk
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
interface Management1
!
interface Vlan1001
   ip address 192.168.101.2/24
!
ip routing
!
ip route 0.0.0.0/0 192.168.101.1
!
end

```

