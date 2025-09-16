# Домашнее задание 6 Overlay. VXLAN. Multihoming

## Цель: Настроить отказоустойчивое подключение клиентов с использованием EVPN Multihoming.


**Описание/Пошаговая инструкция выполнения домашнего задания:**
В этой самостоятельной работе мы ожидаем, что вы самостоятельно:

1. Подключите клиентов 2-я линками к различным Leaf
2. Настроите агрегированный канал со стороны клиента.
3. Настроите multihoming для работы в Overlay сети. Если используете Cisco NXOS - vPC, если иной вендор - то ESI LAG (либо MC-LAG с поддержкой VXLAN)
4. Зафиксируете в документации - план работы, адресное пространство, схему сети, конфигурацию устройств
5. Опционально - протестировать отказоустойчивость - убедиться, что связнность не теряется при отключении одного из линков

# Выполнение Задания

Настраиваем на базе Underlay OSPF.
Конфигурация [тут](https://github.com/igorvoroshkevich-93/Network-course/blob/main/Lab06_Overlay_EVPN_L3/Ospf_full_conf.md)
Работоспособность подтверждена [тут](https://github.com/igorvoroshkevich-93/Network-course/blob/main/Lab02_Underlay_OSPF/README.md)

## Новая схема сети

Добавился Leaf, 4х должно хватить.

![BGP_EVE.png](BGP_EVE.png)

### План распределения следующий

***Берем за основу приватную подсеть 10.0.0.0/8 и из нее берем подсети согласно следущей схеме распределения.***

**10.Dn.Sn.Cn/24**

***немного меняем план***

где:

Dn - зарезервированные дипазоны для Дата-центра, Sn - номер Spine , Сn - порядковый номер

Резервируем значения Dn

0 - loopback 1 - понадобится нам для Underlay, как router-id, маска /32, его же Используем для сессий в Overlay

1 - loopback 2 - Заранее зарезервируем диапазон для VxLan VTEP для MLAG(одинаковый для Leaf-pair, берем в последнем октете значение наименьшего Leaf от Loopback0 ) , маска /32

2 - interconnect - используем подход по /31, чтобы не брать на себя риски unnumbered подхода, ну и мне просто привычнее, так как все сессии у нас в Underlay будут p2p, для Примера: OSPF как для Underlay, мы избежим сходимости сети с распределением DR/BDR, все сессии у нас будут в состоняии Full

***3 - peerlink interconnect - для построение связности для пирлинков, третий и 4й октет возьмем по следующему принципу - 10.3.L.0-1 - для heartbeat, 10.3.L.2-3 - peer-to-peer OSPF, где L - номер Leaf-pair начиная с "0"***

4-7 - на данный момент просто зарезервируем, пока не понятно для чего тратить, либо сервисы либо mgmt, пока не трогаем

Sn - нумеруем по лучшим правилам, начиная с "0" (для loopback нумрацию spine не используем, там просто резервируем в Cn первые 16 адрсов на Spine, а с 32 по 64 на Leaf)

На портах, в виду того, что всх интерфейсы у нас L3-типа, берем единый влан, сразу зарезервируем парочку

**4090 - используем, берем для peer-link**

**903, 904 - клиентские - делим, совместим Symmetric IRB с пердыдущей лабораторной работы, пригодится потом для проверки L3** 

Итого - наш зарезрвированный диапозон для этого условного ЦОД будт 900-999


***Клиентскую подсеть берем следующую:***

172.16.N.0/24

Где N - номер клиентского домена, по заданию надо чтобы был не один, начинаем нумеровать, начиная с "0"


### Таблица распределения адресного пространства

|Device    |Port      |IPv4                               |VLAN|Link                             |Comment         |
|----------|----------|-----------------------------------|----|---------------------------------|----------------|
|Spine_1   |eth1      |  10.2.0.0/31                      | --  |Spine_1 eth1 – eth1 Leaf_1      |  Interconnect  |
|Spine_1   |eth2      |  10.2.0.2/31                      | --  |Spine_1 eth2 – eth1 Leaf_2      |  Interconnect  |
|Spine_1   |eth3      |  10.2.0.4/31                      | --  |Spine_1 eth3 – eth1 Leaf_3      |  Interconnect  |
|Spine_1   |eth4      |  10.2.0.6/31                      | --  |Spine_2 eth4 – eth1 Leaf_4      |  Interconnect  |
|Spine_1   |loopback0 |  10.0.0.0/32                      | --  | None                           |  Loopback      |
|Spine_2   |eth1      |  10.2.1.0/31                      | --  |Spine_2 eth1 – eth2 Leaf_1      |  Interconnect  |
|Spine_2   |eth2      |  10.2.1.2/31                      | --  |Spine_2 eth2 – eth2 Leaf_2      |  Interconnect  |
|Spine_2   |eth3      |  10.2.1.4/31                      | --  |Spine_2 eth3 – eth2 Leaf_3      |  Interconnect  |
|Spine_2   |eth4      |  10.2.1.6/31                      | --  |Spine_2 eth4 – eth2 Leaf_4      |  Interconnect  |
|Spine_2   |loopback0 |  10.0.0.1/32                      | --  | None                           |  Loopback      |

|Leaf_1    |eth1      |  10.2.0.1/31                      | --  |Leaf_1 eth1 – eth1 Spine_1      |  Interconnect  |
|Leaf_1    |eth2      |  10.2.1.1/31                      | --  |Leaf_1 eth2 – eth1 Spine_2      |  Interconnect  |
|Leaf_1    |eth3      |  10.3.0.0/31                      | --  |Leaf_1 eth3 – eth3  Leaf_2      |  Keepalive     |
|Leaf_1    |eth4      |  None                             |4090 |Leaf_1 po10 – po10  Leaf_2      |  Peer-link     |
|Leaf_1    |eth5      |  None                             |4090 |Leaf_1 po10 – po10  Leaf_2      |  Peer-link     |
|Leaf_1    |vlanIf903 |  172.16.0.1/24                    |903  | Po20                           |  VRF-VRRP      |
|Leaf_1    |vlanIf903 |  172.16.0.2/24                    |903  | Po20                           |  VRF-IP Leaf   |
|Leaf_1    |vlanIf4090|  10.3.0.2/31                      |4090 | Leaf_1 po10 Leaf_2 po10        |  Peer-link     |
|Leaf_1    |eth7      |  None                             |903  |Leaf_1 po20  – po10 Client_1    |  Client        |
|Leaf_1    |loopback0 |  10.0.0.32/32                     | --  | None                           |  Loopback      |
|Leaf_1    |loopback1 |  10.1.0.32/32                     | --  | None                           |  Loopback      |
|Leaf_2    |eth1      |  10.2.0.3/31                      | --  |Leaf_2 eth1 – eth2 Spine_1      |  Interconnect  |
|Leaf_2    |eth2      |  10.2.1.3/31                      | --  |Leaf_2 eth2 – eth2 Spine_2      |  Interconnect  |
|Leaf_2    |eth3      |  10.3.0.1/31                      | --  |Leaf_2 eth3 – eth3  Leaf_1      |  Keepalive     |
|Leaf_2    |eth4      |  None                             |4090 |Leaf_2 po10 – po10  Leaf_1      |  Peer-link     |
|Leaf_2    |eth5      |  None                             |4090 |Leaf_2 po10 – po10  Leaf_1      |  Peer-link     |
|Leaf_2    |vlanIf903 |  172.16.0.1/24                    |903  | Po20                           |  VRF-VRRP      |
|Leaf_2    |vlanIf903 |  172.16.0.3/24                    |903  | Po20                           |  VRF-IP Leaf   |
|Leaf_2    |vlanIf4090|  10.3.0.3/31                      |4090 | Leaf_2 po10 Leaf_1 po10        |  Peer-link     |
|Leaf_2    |eth7      |  None                             |903  |Leaf_2 po20  – po10 Client_1    |  Client        |
|Leaf_2    |loopback0 |  10.0.0.33/32                     | --  | None                           |  Loopback      |
|Leaf_2    |loopback1 |  10.1.0.32/32                     | --  | None                           |  Loopback      |

|Leaf_3    |eth1      |  10.2.0.5/31                      | --  |Leaf_1 eth1 – eth1 Spine_1      |  Interconnect  |
|Leaf_3    |eth2      |  10.2.1.5/31                      | --  |Leaf_1 eth2 – eth1 Spine_2      |  Interconnect  |
|Leaf_3    |eth3      |  10.3.1.0/31                      | --  |Leaf_1 eth3 – eth3  Leaf_2      |  Keepalive     |
|Leaf_3    |eth4      |  None                             |4090 |Leaf_1 po10 – po10  Leaf_2      |  Peer-link     |
|Leaf_3    |eth5      |  None                             |4090 |Leaf_1 po10 – po10  Leaf_2      |  Peer-link     |
|Leaf_3    |vlanIf903 |  172.16.1.1/24                    |904  | Po20                           |  VRF-VRRP      |
|Leaf_3    |vlanIf903 |  172.16.1.2/24                    |904  | Po20                           |  VRF-IP Leaf   |
|Leaf_3    |vlanIf4090|  10.3.1.2/31                      |4090 | Leaf_1 po10 Leaf_2 po10        |  Peer-link     |
|Leaf_3    |eth7      |  None                             |903  |Leaf_1 po20  – po10 Client_1    |  Client        |
|Leaf_3    |loopback0 |  10.0.0.34/32                     | --  | None                           |  Loopback      |
|Leaf_3    |loopback1 |  10.1.0.34/32                     | --  | None                           |  Loopback      |
|Leaf_4    |eth1      |  10.2.0.7/31                      | --  |Leaf_2 eth1 – eth2 Spine_1      |  Interconnect  |
|Leaf_4    |eth2      |  10.2.1.7/31                      | --  |Leaf_2 eth2 – eth2 Spine_2      |  Interconnect  |
|Leaf_4    |eth3      |  10.3.1.1/31                      | --  |Leaf_2 eth3 – eth3  Leaf_1      |  Keepalive     |
|Leaf_4    |eth4      |  None                             |4090 |Leaf_2 po10 – po10  Leaf_1      |  Peer-link     |
|Leaf_4    |eth5      |  None                             |4090 |Leaf_2 po10 – po10  Leaf_1      |  Peer-link     |
|Leaf_4    |vlanIf903 |  172.16.1.1/24                    |904  | Po20                           |  VRF-VRRP      |
|Leaf_4    |vlanIf903 |  172.16.1.3/24                    |904  | Po20                           |  VRF-IP Leaf   |
|Leaf_4    |vlanIf4090|  10.3.1.3/31                      |4090 | Leaf_2 po10 Leaf_1 po10        |  Peer-link     |
|Leaf_4    |eth7      |  None                             |903  |Leaf_2 po20  – po10 Client_1    |  Client        |
|Leaf_4    |loopback0 |  10.0.0.35/32                     | --  | None                           |  Loopback      |
|Leaf_4    |loopback1 |  10.1.0.34/32                     | --  | None                           |  Loopback      |

|Client_1  |eth0     |  172.16.0.10/24                    |903  |Client_1 po10 – po20  Leaf_1_2  |  Client        |
|Client_2  |eth0     |  172.16.1.10/24                    |904  |Client_2 po10 – po20  Leaf_3_4  |  Client        |

## AS-Number

**eBGP**

Начинаем Распределять, в виду того, что сессии у нас должны быть уникальны. Spine's - берем по следующему плану

65XAA Где X - номер POD, делаем вид, что у нас будет не более 8 подов, так как "0" мы резервируем для Super-Spine, а 1-9 для Spine AA - уникальный номер, для Spine резервируем значение "00", для Leaf's берем порядковый номер Leaf-а, 

к примеру Leaf-1 будет иметь номер 65101, а Spine-ы - 65100

## План последующей конфигурации


Вланы на L3 интерфейсах снова не используем, пишет что версия образа не поддерживает, но, в клиентов access-ом подаем уже вланы, нам же нужно проверить связность.

Шаги 1-8 пропускаем, делаем вид, что Underlay у нас рабочий, конфиг приложен.

1. Подключаемся к оборудованию.
2. Настраиваем hostname.
3. Так как у нас Arista - глобально включаем routing.
4. Конфигурируем интерфейсы согласно плана(eth, loopback).
5. Проверяем интерконнекты командой ping - убеждаемся в связности.
6. Настраиваем BFD.
7. Включаем процесс ISIS глобально и конфигурируем его(Authorization, zones, etc.)
8. Проеряем маршрутные таблицы, трассировки, связность лупбеков.
9. Поднимаем Vlan, подаем их в сторону клиентов
10. Настраиваем EVPN BGP, соседи, рассширенное комьюнити, авторизация.
11. Проверяем соседство, маршруты.
12. Инициализируем binding vlan to vxlan, предварительно создав интерфейс для инкапсуляции/деинкапсуляции
13. Создаем VRF для Symmetric IRB на всех Leaf
14. Проверяем связность клиентских хостов, прикладываем везде где можно дампы трафика.

# Приступаем к выполнению

Обнулились, чистое оборудование, никто ничего не настраивал, пустое железо, просто прокатываем конфиг Underlay

## Конфигурация VxLAN EVPN для L3 связанности

Вот тут начинается самое интересное.
BFD в этот раз не используем, разваливается вся лаба, надо таймеры задирать, крутил вплоть до 1000 ms - все равно рассыпалась, пусть будет только на Underlay

### Прольем сначала конфиг на Spine-1

```
Spine-1(config)#ip prefix-list Leaf_LB seq 10 permit 10.0.0.32/32
Spine-1(config)#ip prefix-list Leaf_LB seq 20 permit 10.0.0.33/32
Spine-1(config)#ip prefix-list Leaf_LB seq 30 permit 10.0.0.34/32
Spine-1(config)#ip prefix-list Leaf_LB seq 40 permit 10.1.0.32/32
Spine-1(config)#ip prefix-list Leaf_LB seq 50 permit 10.1.0.33/32
Spine-1(config)#ip prefix-list Leaf_LB seq 60 permit 10.1.0.34/32
Spine-1(config)#ip prefix-list Spine_LB seq 10 permit 10.0.0.0/32
Spine-1(config)#ip prefix-list Spine_LB seq 20 permit 10.1.0.0/32
Spine-1(config)#!
Spine-1(config)#route-map Loopback permit 10
Spine-1(config-route-map-Loopback)#   match ip address prefix-list Spine_LB
Spine-1(config-route-map-Loopback)#route-map Loopback permit 20
Spine-1(config-route-map-Loopback)#   match ip address prefix-list Leaf_LB
Spine-1(config-route-map-Loopback)#peer-filter ASN_Leafs
Spine-1(config-peer-filter-ASN_Leafs)#   10 match as-range 65101-65199 result accept
Spine-1(config-peer-filter-ASN_Leafs)#!
Spine-1(config-peer-filter-ASN_Leafs)#router bgp 65100
Spine-1(config-router-bgp)#   router-id 10.1.0.0
Spine-1(config-router-bgp)#   maximum-paths 2 ecmp 2
Spine-1(config-router-bgp)#   bgp listen range 10.1.0.0/24 peer-group EVPN_Overlay peer-filter ASN_Leafs
Spine-1(config-router-bgp)#   neighbor EVPN_Overlay peer group
Spine-1(config-router-bgp)#   neighbor EVPN_Overlay remote-as 65100
Spine-1(config-router-bgp)#   neighbor EVPN_Overlay update-source Loopback1
Spine-1(config-router-bgp)#   neighbor EVPN_Overlay route-reflector-client
Spine-1(config-router-bgp)#   neighbor EVPN_Overlay send-community extended
Spine-1(config-router-bgp)#   neighbor EVPN_Overlay ebgp-multihop 10
Spine-1(config-router-bgp)#   neighbor EVPN_Overlay timers 3 9
Spine-1(config-router-bgp)#   neighbor EVPN_Overlay password Otus_Overlay
Spine-1(config-router-bgp)#   redistribute connected route-map Loopback
Spine-1(config-router-bgp)#   address-family evpn
Spine-1(config-router-bgp-af)#      neighbor EVPN_Overlay activate
Spine-1(config-router-bgp-af)#end

```

### Включим захват трафика на интерфесах лифов в сторону этого Spine

Проливаем конфиг на Leaf's и смотрим что там у нас творится.

**Leaf-1**
```
Leaf-1(config)#router bgp 65101
Leaf-1(config-router-bgp)#   router-id 10.1.0.32
Leaf-1(config-router-bgp)#   maximum-paths 2 ecmp 2
Leaf-1(config-router-bgp)#   neighbor EVPN_Overlay peer group
Leaf-1(config-router-bgp)#   neighbor EVPN_Overlay remote-as 65100
Leaf-1(config-router-bgp)#   neighbor EVPN_Overlay update-source Loopback1
Leaf-1(config-router-bgp)#   neighbor EVPN_Overlay send-community extended
Leaf-1(config-router-bgp)#   neighbor EVPN_Overlay ebgp-multihop 10
Leaf-1(config-router-bgp)#   neighbor EVPN_Overlay timers 3 9
Leaf-1(config-router-bgp)#   neighbor EVPN_Overlay password Otus_Overlay
Leaf-1(config-router-bgp)#   neighbor 10.1.0.0 peer group EVPN_Overlay
Leaf-1(config-router-bgp)#   neighbor 10.1.0.1 peer group EVPN_Overlay
Leaf-1(config-router-bgp)#   redistribute connected route-map Loopback
Leaf-1(config-router-bgp)#   !
Leaf-1(config-router-bgp)#   address-family evpn
Leaf-1(config-router-bgp-af)#      neighbor EVPN_Overlay activate
Leaf-1(config-router-bgp-af)#end

```

**Leaf-2**
```
Leaf-2(config)#router bgp 65102
Leaf-2(config-router-bgp)#   router-id 10.1.0.33
Leaf-2(config-router-bgp)#   maximum-paths 2 ecmp 2
Leaf-2(config-router-bgp)#   neighbor EVPN_Overlay peer group
Leaf-2(config-router-bgp)#   neighbor EVPN_Overlay remote-as 65100
Leaf-2(config-router-bgp)#   neighbor EVPN_Overlay update-source Loopback1
Leaf-2(config-router-bgp)#   neighbor EVPN_Overlay send-community extended
Leaf-2(config-router-bgp)#   neighbor EVPN_Overlay ebgp-multihop 10
Leaf-2(config-router-bgp)#   neighbor EVPN_Overlay timers 3 9
Leaf-2(config-router-bgp)#   neighbor EVPN_Overlay password Otus_Overlay
Leaf-2(config-router-bgp)#   neighbor 10.1.0.0 peer group EVPN_Overlay
Leaf-2(config-router-bgp)#   neighbor 10.1.0.1 peer group EVPN_Overlay
Leaf-2(config-router-bgp)#   redistribute connected route-map Loopback
Leaf-2(config-router-bgp)#   !
Leaf-2(config-router-bgp)#   address-family evpn
Leaf-2(config-router-bgp-af)#      neighbor EVPN_Overlay activate
Leaf-2(config-router-bgp-af)#end

```

**Leaf-3**
```
Leaf-3(config)#router bgp 65102
Leaf-3(config-router-bgp)#   router-id 10.1.0.34
Leaf-3(config-router-bgp)#   maximum-paths 2 ecmp 2
Leaf-3(config-router-bgp)#   neighbor EVPN_Overlay peer group
Leaf-3(config-router-bgp)#   neighbor EVPN_Overlay remote-as 65100
Leaf-3(config-router-bgp)#   neighbor EVPN_Overlay update-source Loopback1
Leaf-3(config-router-bgp)#   neighbor EVPN_Overlay send-community extended
Leaf-3(config-router-bgp)#   neighbor EVPN_Overlay ebgp-multihop 10
Leaf-3(config-router-bgp)#   neighbor EVPN_Overlay timers 3 9
Leaf-3(config-router-bgp)#   neighbor EVPN_Overlay password Otus_Overlay
Leaf-3(config-router-bgp)#   neighbor 10.1.0.0 peer group EVPN_Overlay
Leaf-3(config-router-bgp)#   neighbor 10.1.0.1 peer group EVPN_Overlay
Leaf-3(config-router-bgp)#   redistribute connected route-map Loopback
Leaf-3(config-router-bgp)#   !
Leaf-3(config-router-bgp)#   address-family evpn
Leaf-3(config-router-bgp-af)#      neighbor EVPN_Overlay activate
Leaf-3(config-router-bgp-af)#
Leaf-3(config-router-bgp-af)#
Leaf-3(config-router-bgp-af)#end

```

Видим, как побежали пакеты, устанавливается соседство

**Leaf-1 to Spine-1**

![EVPN_L3_full-1.png](EVPN_L3_full-1.png)

**Leaf-2 to Spine-1**

![EVPN_L3_full-2.png](EVPN_L3_full-2.png)

**Leaf-3 to Spine-1**

![EVPN_L3_full-3.png](EVPN_L3_full-3.png)

Смотрим таблицу соседства и что у нас по роутам

**Leaf-1**

![EVPN_L3_leaf-1-1.png](EVPN_L3_leaf-1-1.png)

**Leaf-2**

![EVPN_L3_leaf-2-1.png](EVPN_L3_leaf-2-1.png)

**Leaf-3**

![EVPN_L3_leaf-3-1.png](EVPN_L3_leaf-3-1.png)

**Spine-1**

![EVPN_L3_spine-1-1.png](EVPN_L3_spine-1-1.png)

***Cоседство установилось, но роутов нет, проливаем Spine-2***

**Spine-2**

```
Spine-2(config)#ip prefix-list Leaf_LB seq 20 permit 10.0.0.33/32
Spine-2(config)#ip prefix-list Leaf_LB seq 30 permit 10.0.0.34/32
Spine-2(config)#ip prefix-list Leaf_LB seq 40 permit 10.1.0.32/32
Spine-2(config)#ip prefix-list Leaf_LB seq 50 permit 10.1.0.33/32
Spine-2(config)#ip prefix-list Leaf_LB seq 60 permit 10.1.0.34/32
Spine-2(config)#ip prefix-list Spine_LB seq 10 permit 10.0.0.0/32
Spine-2(config)#ip prefix-list Spine_LB seq 20 permit 10.1.0.0/32
Spine-2(config)#!
Spine-2(config)#route-map Loopback permit 10
Spine-2(config-route-map-Loopback)#   match ip address prefix-list Spine_LB
Spine-2(config-route-map-Loopback)#!
Spine-2(config-route-map-Loopback)#route-map Loopback permit 20
Spine-2(config-route-map-Loopback)#   match ip address prefix-list Leaf_LB
Spine-2(config-route-map-Loopback)#!
Spine-2(config-route-map-Loopback)#peer-filter ASN_Leafs
Spine-2(config-peer-filter-ASN_Leafs)#   10 match as-range 65101-65199 result accept
Spine-2(config-peer-filter-ASN_Leafs)#!
Spine-2(config-peer-filter-ASN_Leafs)#router bgp 65100
Spine-2(config-router-bgp)#   router-id 10.1.0.1
Spine-2(config-router-bgp)#   maximum-paths 2 ecmp 2
Spine-2(config-router-bgp)#   bgp listen range 10.1.0.0/24 peer-group EVPN_Overlay peer-filter ASN_Leafs
Spine-2(config-router-bgp)#   neighbor EVPN_Overlay peer group
Spine-2(config-router-bgp)#   neighbor EVPN_Overlay remote-as 65100
Spine-2(config-router-bgp)#   neighbor EVPN_Overlay update-source Loopback1
Spine-2(config-router-bgp)#   neighbor EVPN_Overlay route-reflector-client
Spine-2(config-router-bgp)#   neighbor EVPN_Overlay send-community extended
Spine-2(config-router-bgp)#   neighbor EVPN_Overlay ebgp-multihop 10
Spine-2(config-router-bgp)#   neighbor EVPN_Overlay timers 3 9
Spine-2(config-router-bgp)#   neighbor EVPN_Overlay password Otus_Overlay
Spine-2(config-router-bgp)#   redistribute connected route-map Loopback
Spine-2(config-router-bgp)#   !
Spine-2(config-router-bgp)#   address-family evpn
Spine-2(config-router-bgp-af)#      neighbor EVPN_Overlay activate
Spine-2(config-router-bgp-af)#   !
Spine-2(config-router-bgp-af)#!
Spine-2(config-router-bgp-af)#end

```

Ну и смотрим как тут дела

![EVPN_L3_spine-2-1.png](EVPN_L3_spine-2-1.png)

***Создаем Vxlan, поднимаем VRF смотрим роуты и состедство***

Проливаем конфиг

**Leaf-1**
```
Leaf-1(config)#vlan 903
Leaf-1(config-vlan-903)#   name Clients_903
Leaf-1(config-vlan-903)#!
Leaf-1(config-vlan-903)#vrf instance Otus_Symmetric_L3
Leaf-1(config-vrf-Otus_Symmetric_L3)#!
Leaf-1(config-vrf-Otus_Symmetric_L3)#ip routing vrf Otus_Symmetric_L3
Leaf-1(config)#!
Leaf-1(config)#int vlan 903
Leaf-1(config-if-Vl903)#   vrf Otus_Symmetric_L3
Leaf-1(config-if-Vl903)#   ip address virtual 172.16.0.1/24
Leaf-1(config-if-Vl903)#!
Leaf-1(config-if-Vl903)#Interface Ethernet7
Leaf-1(config-if-Et7)#   switchport mode access
Leaf-1(config-if-Et7)#   switchport access vlan 903
Leaf-1(config)#int vxlan1
Leaf-1(config-if-Vx1)#  vxlan source-int lo1
Leaf-1(config-if-Vx1)#  vxlan udp-port 4789
Leaf-1(config-if-Vx1)#  vxlan learn-restrict any
Leaf-1(config-if-Vx1)#  vxlan vlan 903 vni 10903
Leaf-1(config-if-Vx1)#  vxlan vrf Otus_Symmetric_L3 vni 10999

Leaf-1(config)#router bgp 65101
Leaf-1(config-router-bgp)#vlan 903
Leaf-1(config-macvrf-903)#      rd 10.1.0.32:10903
Leaf-1(config-macvrf-903)#      route-target both 65100:10903
Leaf-1(config-macvrf-903)#      redistribute learned
Leaf-1(config-macvrf-903)#   vrf Otus_Symmetric_L3
Leaf-1(config-router-bgp-vrf-Otus_Symmetric_L3)#      rd 10.1.0.32:10999
Leaf-1(config-router-bgp-vrf-Otus_Symmetric_L3)#      route-target import evpn 65100:10999
Leaf-1(config-router-bgp-vrf-Otus_Symmetric_L3)#      route-target export evpn 65100:10999
Leaf-1(config-router-bgp-vrf-Otus_Symmetric_L3)#      redistribute learned
Leaf-1(config-router-bgp-vrf-Otus_Symmetric_L3)#      redistribute connected

```

**Leaf-2**
```
Leaf-2(config)#vlan 904
Leaf-2(config-vlan-904)#   name Clients_904
Leaf-2(config-vlan-904)#!
Leaf-2(config-vlan-904)#vrf instance Otus_Symmetric_L3
Leaf-2(config-vrf-Otus_Symmetric_L3)#!
Leaf-2(config-vrf-Otus_Symmetric_L3)#ip routing vrf Otus_Symmetric_L3
Leaf-2(config)#!
Leaf-2(config)#int vlan 904
Leaf-2(config-if-Vl904)#   vrf Otus_Symmetric_L3
Leaf-2(config-if-Vl904)#   ip address virtual 172.16.1.1/24
Leaf-2(config-if-Vl904)#Interface Ethernet7
Leaf-2(config-if-Et7)#   switchport mode access
Leaf-2(config-if-Et7)#   switchport access vlan 904
Leaf-2(config-if-Et7)#int vxlan1
Leaf-2(config-if-Vx1)#  vxlan source-int lo1
Leaf-2(config-if-Vx1)#  vxlan udp-port 4789
Leaf-2(config-if-Vx1)#  vxlan learn-restrict any
Leaf-2(config-if-Vx1)#  vxlan vlan 904 vni 10904
Leaf-2(config)#router bgp 65102
Leaf-2(config-router-bgp)#vlan 904
Leaf-2(config-macvrf-904)#      rd 10.1.0.33:10904
Leaf-2(config-macvrf-904)#      route-target both 65100:10904
Leaf-2(config-macvrf-904)#      redistribute learned
Leaf-2(config-macvrf-904)#   vrf Otus_Symmetric_L3
Leaf-2(config-router-bgp-vrf-Otus_Symmetric_L3)#      rd 10.1.0.33:10999
Leaf-2(config-router-bgp-vrf-Otus_Symmetric_L3)#      route-target import evpn 65100:10999
Leaf-2(config-router-bgp-vrf-Otus_Symmetric_L3)#      route-target export evpn 65100:10999
Leaf-2(config-router-bgp-vrf-Otus_Symmetric_L3)#      redistribute connected
```

**Leaf-3**
```
Leaf-3(config)#vlan 905
Leaf-3(config-vlan-905)#   name Clients_905
Leaf-3(config-vlan-905)#!
Leaf-3(config-vlan-905)#vlan 906
Leaf-3(config-vlan-906)#   name Clients_906
Leaf-3(config-vlan-906)#!
Leaf-3(config-vlan-906)#vrf instance Otus_Symmetric_L3
Leaf-3(config-vrf-Otus_Symmetric_L3)#ip routing vrf Otus_Symmetric_L3
Leaf-3(config)#int vlan 905
Leaf-3(config-if-Vl905)#   vrf Otus_Symmetric_L3
Leaf-3(config-if-Vl905)#   ip address virtual 172.16.2.1/24
Leaf-3(config-if-Vl905)#!
Leaf-3(config-if-Vl905)#int vlan 906
Leaf-3(config-if-Vl906)#   vrf Otus_Symmetric_L3
Leaf-3(config-if-Vl906)#   ip address virtual 172.16.3.1/24
Leaf-3(config-if-Vl906)#!
Leaf-3(config-if-Vl906)#Interface Ethernet7
Leaf-3(config-if-Et7)#   switchport mode access
Leaf-3(config-if-Et7)#   switchport access vlan 905
Leaf-3(config-if-Et7)#!
Leaf-3(config-if-Et7)#Interface Ethernet8
Leaf-3(config-if-Et8)#   switchport mode access
Leaf-3(config-if-Et8)#   switchport access vlan 906
Leaf-3(config-if-Et8)#
Leaf-3(config-if-Et8)#int vxlan1
Leaf-3(config-if-Vx1)#  vxlan source-int lo1
Leaf-3(config-if-Vx1)#  vxlan udp-port 4789
Leaf-3(config-if-Vx1)#  vxlan learn-restrict any
Leaf-3(config-if-Vx1)#  vxlan vlan 905 vni 10905
Leaf-3(config-if-Vx1)#  vxlan vlan 906 vni 10906
Leaf-3(config-if-Vx1)#  vxlan vrf Otus_Symmetric_L3 vni 10999
Leaf-3(config-if-Vx1)#exit
Leaf-3(config)#router bgp 65103
Leaf-3(config-router-bgp)#vlan 905
Leaf-3(config-macvrf-905)#      rd 10.1.0.34:10905
Leaf-3(config-macvrf-905)#      route-target both 65100:10905
Leaf-3(config-macvrf-905)#      redistribute learned
Leaf-3(config-macvrf-905)#   vlan 906
Leaf-3(config-macvrf-906)#      rd 10.1.0.34:10906
Leaf-3(config-macvrf-906)#      route-target both 65100:10906
Leaf-3(config-macvrf-906)#      redistribute learned
Leaf-3(config-macvrf-906)#   vrf Otus_Symmetric_L3
Leaf-3(config-router-bgp-vrf-Otus_Symmetric_L3)#      rd 10.1.0.34:10999
Leaf-3(config-router-bgp-vrf-Otus_Symmetric_L3)#      route-target import evpn 65100:10999
Leaf-3(config-router-bgp-vrf-Otus_Symmetric_L3)#      route-target export evpn 65100:10999
Leaf-3(config-router-bgp-vrf-Otus_Symmetric_L3)#      redistribute connected
Leaf-3(config-router-bgp-vrf-Otus_Symmetric_L3)#end

```

Смотрим роуты и соседство

**Spine-1**

CLI

![EVPN_L3_spine-1-2.png](EVPN_L3_spine-1-2.png)

**Spine-2**

CLI

![EVPN_L3_spine-2-2.png](EVPN_L3_spine-2-2.png)

**Leaf-1**

Dump

![EVPN_L3_full-1-1.png](EVPN_L3_full-1-1.png)

![EVPN_L3_full-1-2.png](EVPN_L3_full-1-2.png)

CLI

![EVPN_L3_leaf-1-2.png](EVPN_L3_leaf-1-2.png)

**Leaf-2**

Dump

![EVPN_L3_full-2-1.png](EVPN_L3_full-2-1.png)

![EVPN_L3_full-2-2.png](EVPN_L3_full-2-2.png)

CLI

![EVPN_L3_leaf-2-2.png](EVPN_L3_leaf-2-2.png)

**Leaf-3**

Dump

![EVPN_L3_full-3-1.png](EVPN_L3_full-3-1.png)

![EVPN_L3_full-3-2.png](EVPN_L3_full-3-2.png)

CLI

![EVPN_L3_leaf-3-2.png](EVPN_L3_leaf-3-2.png)


### Поднимаем конфигурацию на клиентах, проверяем связность.

Конфигурацию писать не вижу смысла, она будет в общем файле, просто пингуем и смотрим в дамп трафика

**Client-1 PING+DUMP**

*Ping*

![EVPN_client_1_L3_ping.png](EVPN_client_1_L3_ping.png)

*Dump*

![EVPN_client-1_L3-1_dump.png](EVPN_client-1_L3-1_dump.png)

**Неожиданно отработал ecmp в лабе**

![EVPN_client-1_L3-2_dump.png](EVPN_client-1_L3-2_dump.png)

**Client-2 PING+DUMP**

*Ping*

![EVPN_client_2_L3_ping.png](EVPN_client_2_L3_ping.png)

*Dump*

![EVPN_client-2_L3-1_dump.png](EVPN_client-2_L3-1_dump.png)

**Client-3 PING+DUMP**

***Тут начинается интересная вещь, в дампе нет пингов между 3 и 4 клиентами, просто потому, что они на одном лифе, ловить нужно в другом месте, но мы уже убедились, что по фабрике связность есть выше***

*Ping*

![EVPN_client_3_L3_ping.png](EVPN_client_3_L3_ping.png)

*Dump*

![EVPN_client-3_L3-1_dump.png](EVPN_client-3_L3-1_dump.png)

**Client-4 PING+DUMP**

*Ping*

![EVPN_client_4_L3_ping.png](EVPN_client_4_L3_ping.png)

*Dump*

![EVPN_client-4_L3-1_dump.png](EVPN_client-4_L3-1_dump.png)

### Все работает, связность есть

#### Скрины соседства роутов BFD, VxLan(на Leaf)

**Spine-1**

![EVPN_L3_spine-1-3.png](EVPN_L3_spine-1-3.png)

**Spine-2**

![EVPN_L3_spine-1-3.png](EVPN_L3_spine-1-3.png)

**Leaf-1**

![EVPN_L3_leaf-1-3.png](EVPN_L3_leaf-1-3.png)

**Leaf-2**

![EVPN_L3_leaf-2-3.png](EVPN_L3_leaf-2-3.png)

**Leaf-3**

![EVPN_L3_leaf-3-3.png](EVPN_L3_leaf-3-3.png)

Конфигурация [общим файлом](https://github.com/igorvoroshkevich-93/Network-course/blob/main/Lab06_Overlay_EVPN_L3/EVPN_L3_full_conf.md)

