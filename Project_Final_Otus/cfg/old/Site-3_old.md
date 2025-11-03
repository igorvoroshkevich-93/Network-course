**Agg-1**

```
no aaa root
!
transceiver qsfp default-mode 4x10G
!
service routing protocols model multi-agent
!
hostname Spine-1-Site-3
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
   switchport mode trunk
!
interface Ethernet6
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
ip routing
!
end

```


**TOR-2**

```
no aaa root
!
transceiver qsfp default-mode 4x10G
!
service routing protocols model multi-agent
!
hostname Leaf-2-Site-3
!
spanning-tree mode mstp
!
vlan 1002
   name Vlan-1002-Client-3
!
interface Ethernet1
   shutdown
!
interface Ethernet2
   switchport mode trunk
!
interface Ethernet3
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
no ip routing
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
hostname Leaf-3-Site-3
!
spanning-tree mode mstp
!
vlan 1003
   name Vlan-1003-Client-4
!
interface Ethernet1
   shutdown
!
interface Ethernet2
   switchport mode trunk
!
interface Ethernet3
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
no ip routing
!
end


```

**Client-3**

```
no aaa root
!
transceiver qsfp default-mode 4x10G
!
service routing protocols model multi-agent
!
hostname Client-3
!
spanning-tree mode mstp
!
vlan 1002
   name Client-3_OLD
!
interface Ethernet1
   switchport trunk allowed vlan 1002
   switchport mode trunk
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
!
interface Ethernet7
!
interface Ethernet8
!
interface Management1
!
interface Vlan1002
   ip address 192.168.102.2/24
!
no ip routing
!
ip route 0.0.0.0/0 192.168.102.1
!
end

```



**Client-4**

```
no aaa root
!
transceiver qsfp default-mode 4x10G
!
service routing protocols model multi-agent
!
hostname Client-4
!
spanning-tree mode mstp
!
vlan 1003
   name Client-4_OLD
!
interface Ethernet1
   switchport trunk allowed vlan 1003
   switchport mode trunk
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
!
interface Ethernet7
!
interface Ethernet8
!
interface Management1
!
interface Vlan1003
   ip address 192.168.103.2/24
!
no ip routing
!
ip route 0.0.0.0/0 192.168.103.1
!
end

```
