
**Leaf-3**

```
!
ip prefix-list Client_nets seq 10 permit 172.16.0.0/24
ip prefix-list Client_nets seq 20 permit 172.16.2.0/24
!
vrf instance Otus_Gateway
!
ip routing vrf Otus_Gateway
!
route-map RM-ALL-ROUTES permit 10
!
route-map Client_networks permit 10
   match ip address prefix-list Client_nets
!
interface Ethernet7
   no switchport
   vrf Otus_Gateway
   ip address 10.4.0.1/31
!
router bgp 65103
   vrf Otus_BLUE
      route-target export 65100:15111
      route-target import 65100:15999
   vrf Otus_RED
      route-target export 65100:15111
      route-target import 65100:15999
   vrf Otus_Gateway
      router-id 10.4.0.1
      rd 10.4.0.1:15000
      route-target import 65100:15111
      route-target export 65100:15999
      maximum-paths 4 ecmp 64
      neighbor Gateway-OTUS peer group
      neighbor Gateway-OTUS remote-as 65200
      neighbor Gateway-OTUS timers 30 90
      neighbor Gateway-OTUS password Otus_Gateway
      neighbor 10.4.0.0 peer group Gateway-OTUS
      redistribute connected route-map Client_networks
      address-family ipv4
         neighbor Gateway-OTUS activate
!
router general
   vrf Otus_BLUE
      leak routes source-vrf Otus_Gateway subscribe-policy RM-ALL-ROUTES
   !
   vrf Otus_Gateway
      leak routes source-vrf Otus_BLUE subscribe-policy RM-ALL-ROUTES
      leak routes source-vrf Otus_RED subscribe-policy RM-ALL-ROUTES
   !
   vrf Otus_RED
      leak routes source-vrf Otus_Gateway subscribe-policy RM-ALL-ROUTES
!
end
```

**Leaf-4**

```
!
ip prefix-list Client_nets seq 10 permit 172.16.0.0/24
ip prefix-list Client_nets seq 20 permit 172.16.2.0/24
!
vrf instance Otus_Gateway
!
ip routing vrf Otus_Gateway
!
route-map RM-ALL-ROUTES permit 10
!
route-map Client_networks permit 10
   match ip address prefix-list Client_nets
!
interface Ethernet7
   no switchport
   vrf Otus_Gateway
   ip address 10.4.0.3/31
!
router bgp 65104
   vrf Otus_BLUE
      route-target export 65100:15111
      route-target import 65100:15999
   vrf Otus_RED
      route-target export 65100:15111
      route-target import 65100:15999
   vrf Otus_Gateway
      router-id 10.4.0.3
      rd 10.4.0.3:15000
      route-target import 65100:15111
      route-target export 65100:15999
      maximum-paths 4 ecmp 64
      neighbor Gateway-OTUS peer group
      neighbor Gateway-OTUS remote-as 65200
      neighbor Gateway-OTUS timers 30 90
      neighbor Gateway-OTUS password Otus_Gateway
      neighbor 10.4.0.2 peer group Gateway-OTUS
      redistribute connected route-map Client_networks
      address-family ipv4
         neighbor Gateway-OTUS activate
!
router general
   vrf Otus_BLUE
      leak routes source-vrf Otus_Gateway subscribe-policy RM-ALL-ROUTES
   !
   vrf Otus_Gateway
      leak routes source-vrf Otus_BLUE subscribe-policy RM-ALL-ROUTES
      leak routes source-vrf Otus_RED subscribe-policy RM-ALL-ROUTES
   !
   vrf Otus_RED
      leak routes source-vrf Otus_Gateway subscribe-policy RM-ALL-ROUTES
!
end
```


**Gateway-1**

```
hostname Gateway-1
!
interface Ethernet1
   no switchport
   ip address 10.4.0.0/31
!
interface Ethernet2
   no switchport
   ip address 10.4.0.2/31
!
interface loopback0
   ip address 8.8.8.8/32
!
interface loopback1
   ip address 1.1.1.1/32
!
ip prefix-list G_nets seq 10 permit 8.8.8.8/32
ip prefix-list G_nets seq 20 permit 1.1.1.1/32
!
route-map Global_networks permit 10
   match ip address prefix-list G_nets
!
ip prefix-list Client_nets seq 10 permit 172.16.0.0/24
ip prefix-list Client_nets seq 20 permit 172.16.2.0/24
!
route-map Client_networks permit 10
   match ip address prefix-list Client_nets
!
peer-filter ASN_Border_Leafs
   10 match as-range 65103-65104 result accept
!
router bgp 65200
      router-id 10.4.0.0
      maximum-paths 4 ecmp 64
      bgp listen range 10.4.0.0/24 peer-group Gateway-OTUS peer-filter ASN_Border_Leafs
      neighbor Gateway-OTUS peer group
      neighbor Gateway-OTUS timers 30 90
      neighbor Gateway-OTUS password Otus_Gateway
      neighbor Gateway-OTUS default-originate
      neighbor Gateway-OTUS route-map Client_networks in
      neighbor 10.4.0.1 peer group Gateway-OTUS
      neighbor 10.4.0.3 peer group Gateway-OTUS
      redistribute connected route-map Global_networks
      address-family ipv4
         neighbor Gateway-OTUS activate
!
end
```
