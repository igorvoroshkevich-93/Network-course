# Переход от классической трехзвенной архитектуры к полноценной VxLan-фабрике с multisite


## Суть в чем, есть небольшой цод, работает на классической трехзвенчатой архитектуре, но

1) Появился запрос на горизонтальный трафик, заезжает клиент, со своей архитекрутрой, причем не один, ему нужно распределить его инфраструктуру в ЦОД(l2, l3, anycast gateway)

2) Он хочет, как и другие клиенты, георезервирование - получаем multisite

3) Выход на нескольких провайдеров - все нужно, пограничные анонсы, ытобы резервировать аплинки, анонс его сетей

4) Логичный запрос - резервирование серверов (MLAG, ESI-LAG)

## Цели

1) Использовать по максимуму все технологии освоенные в курсе

2) Закрепить на практике и в плане проектирования АБСОЛЮТНО все полученные знания

План такой

1) Смотрим что у нас сейчас, как реализована сеть, какое распределение VLAN, IP, peers

2) Простраиваем поверх текущего решения новое с фабрикой на multisite

3) Планируем работы, осуществляем переход от старого железного, к новому и современному, естественно с заделом на легкое масштабирование и увеличение количества и размеров наших ЦОД

# Выполнение проектной работы

Наметим точки, которые нам требуются

1) Где мы сейчас

2) Куда мы стремимся

3) Как мы к этому придем

## Где мы сейчас ?

Схема текущей сети

![](img/Schema_new.png)

Имеем классическую архитектуру, в трафиком N-S. Терминацией вланов mlag от уровня агрегации доступа и SVI на каждую подсеть. Между Site у нас голый L2 Vlan, с STP в кольцо, что не совсем хорошо, одно плечо простаивает, резервирование есть, но пути прохождения трафика неоптимальные, быстрый переход сложен, нам будет мешать STP

Собираем эту схему, в базовом виде

Конфигурации тут ***

## Куда мы стремимся ?

Видим следующие проблемы:

1) Остутствует корректная возможность изоляции L2 - у нас влан растянутый между site - потенциальные проблемы с STP
2) Все замыкается на GW - у нас весь трафик так или иначе заворачивается на корневой роутер, крутится там и передается дальше, по хорошему, нам нужно дать возможность трафику ходить горизонтально, только при необходимости - между SITE
3) L3-трафик имеет такую зе проблему, все оседает на GW - туда он должен выходить только при ситуации - когда надо передать трафик в условный интернет.

Схема новой сети сети

![](img/Schema.png)

## План перехода

1) Строим новую сеть поверх старой, благо оборудование имеет и портовую емкость для создания Underlay/Overlay
2) Проверяем связность горизонтального трафика, вертикального, L2 и L3 между Site
3) Открепляем вланы от старой системы, прикрепляем их на работу новой фабрики
4) проверяем работоспособность

## Осуществляем переход


1) Простраиваем связность каждого из SITE - проверяем связность внутри каждого из DC

2) Связываем SITE - смотрим связность между ними, как по L2 так и по L3

3) Осуществляем переход

Конфигурационные файлы

Таблица IP


|Device    |Port      |IPv4                               |VLAN|Link                             |Comment         |
|----------|----------|-----------------------------------|----|---------------------------------|----------------|
|Spine_1_1   |eth1      |  10.2.0.0/31                      | --  |Spine_1 eth1 – eth1 Leaf_1      |  Interconnect  |
|Spine_1_1   |eth2      |  10.2.0.2/31                      | --  |Spine_1 eth2 – eth1 Leaf_2      |  Interconnect  |
|Spine_1_1   |eth3      |  10.2.0.4/31                      | --  |Spine_1 eth3 – eth1 Leaf_3      |  Interconnect  |
|Spine_1_1   |eth4      |  10.2.0.6/31                      | --  |Spine_2 eth4 – eth1 Leaf_4      |  Interconnect  |
|Spine_1_1   |loopback0 |  10.0.0.0/32                      | --  | None                           |  Loopback      |
|Spine_2_1   |eth1      |  10.2.1.0/31                      | --  |Spine_2 eth1 – eth2 Leaf_1      |  Interconnect  |
|Spine_2_1   |eth2      |  10.2.1.2/31                      | --  |Spine_2 eth2 – eth2 Leaf_2      |  Interconnect  |
|Spine_2_1   |eth3      |  10.2.1.4/31                      | --  |Spine_2 eth3 – eth2 Leaf_3      |  Interconnect  |
|Spine_2_1   |eth4      |  10.2.1.6/31                      | --  |Spine_2 eth4 – eth2 Leaf_4      |  Interconnect  |
|Spine_2_1   |loopback0 |  10.0.0.1/32                      | --  | None                           |  Loopback      |
|----------|----------|-----------------------------------|-----|--------------------------------|----------------|
|Leaf_1_1    |eth1      |  10.2.0.1/31                      | --  |Leaf_1 eth1 – eth1 Spine_1      |  Interconnect  |
|Leaf_1_1    |eth2      |  10.2.1.1/31                      | --  |Leaf_1 eth2 – eth1 Spine_2      |  Interconnect  |
|Leaf_1_1    |eth3      |  10.3.0.0/31                      | --  |Leaf_1 eth3 – eth3  Leaf_2      |  Keepalive     |
|Leaf_1_1    |eth4      |  None                             |4090 |Leaf_1 po10 – po10  Leaf_2      |  Peer-link     |
|Leaf_1_1    |eth5      |  None                             |4090 |Leaf_1 po10 – po10  Leaf_2      |  Peer-link     |
|Leaf_1_1    |vlanIf4090|  10.3.0.2/31                      |4090 | Leaf_1 po10 Leaf_2 po10        |  Peer-link     |
|Leaf_1_1    |eth7      |  None                             |903  |Leaf_1 po20  – po10 Client_1    |  Client        |
|Leaf_1_1    |loopback0 |  10.0.0.32/32                     | --  | None                           |  Loopback      |
|Leaf_1_1    |loopback1 |  10.1.0.32/32                     | --  | Shared with Leaf_2             |  Loopback      |
|----------|----------|-----------------------------------|-----|--------------------------------|----------------|
|Leaf_2_1    |eth1      |  10.2.0.3/31                      | --  |Leaf_2 eth1 – eth2 Spine_1      |  Interconnect  |
|Leaf_2_1    |eth2      |  10.2.1.3/31                      | --  |Leaf_2 eth2 – eth2 Spine_2      |  Interconnect  |
|Leaf_2_1    |eth3      |  10.3.0.1/31                      | --  |Leaf_2 eth3 – eth3  Leaf_1      |  Keepalive     |
|Leaf_2_1    |eth4      |  None                             |4090 |Leaf_2 po10 – po10  Leaf_1      |  Peer-link     |
|Leaf_2_1    |eth5      |  None                             |4090 |Leaf_2 po10 – po10  Leaf_1      |  Peer-link     |
|Leaf_2_1    |vlanIf4090|  10.3.0.3/31                      |4090 | Leaf_2 po10 Leaf_1 po10        |  Peer-link     |
|Leaf_2_1    |eth7      |  None                             |903  |Leaf_2 po20  – po10 Client_1    |  Client        |
|Leaf_2_1    |loopback0 |  10.0.0.33/32                     | --  | None                           |  Loopback      |
|Leaf_2_1    |loopback1 |  10.1.0.32/32                     | --  | Shared with Leaf_1             |  Loopback      |
|----------|----------|-----------------------------------|-----|--------------------------------|----------------|
|Leaf_3_1    |eth1      |  10.2.0.5/31                      | --  |Leaf_3 eth1 – eth1 Spine_1      |  Interconnect  |
|Leaf_3_1    |eth2      |  10.2.1.5/31                      | --  |Leaf_3 eth2 – eth1 Spine_2      |  Interconnect  |
|Leaf_3_1    |vlanIf903 |  172.16.0.1/24                    |903  | Virtual-IP                     |  VARP          |
|Leaf_3_1    |vlanIf905 |  172.16.2.1/24                    |905  | Virtual-IP                     |  VARP          |
|Leaf_3_1    |eth7      |  172.16.4.1/31                    | --  |Leaf_3 eth7  – eth1 Gateway-1   |  Client        |
|Leaf_3_1    |loopback0 |  10.0.0.34/32                     | --  | None                           |  Loopback      |
|Leaf_3_1    |loopback1 |  10.1.0.34/32                     | --  | Shared with Leaf_4             |  Loopback      |
|----------|----------|-----------------------------------|-----|--------------------------------|----------------|
|Spine_1_2   |eth1      |  10.12.0.0/31                      | --  |Spine_2 eth1 – eth1 Leaf_2      |  Interconnect  |
|Spine_1_2   |eth2      |  10.12.0.2/31                      | --  |Spine_2 eth2 – eth1 Leaf_2      |  Interconnect  |
|Spine_1_2   |eth3      |  10.12.0.4/31                      | --  |Spine_2 eth3 – eth1 Leaf_3      |  Interconnect  |
|Spine_1_2   |eth4      |  10.12.0.6/31                      | --  |Spine_2 eth4 – eth1 Leaf_4      |  Interconnect  |
|Spine_1_2   |loopback0 |  10.10.0.0/32                      | --  | None                           |  Loopback      |
|----------|----------|-----------------------------------|-----|--------------------------------|----------------|
|Leaf_1_2    |eth1      |  10.12.0.1/31                      | --  |Leaf_2 eth1 – eth1 Spine_2      |  Interconnect  |
|Leaf_1_2    |eth2      |  10.12.1.1/31                      | --  |Leaf_2 eth2 – eth1 Spine_2      |  Interconnect  |
|Leaf_1_2    |eth3      |  10.13.0.0/31                      | --  |Leaf_2 eth3 – eth3  Leaf_2      |  Keepalive     |
|Leaf_1_2    |eth4      |  None                             |4090 |Leaf_2 po10 – po10  Leaf_2      |  Peer-link     |
|Leaf_1_2    |eth5      |  None                             |4090 |Leaf_2 po10 – po10  Leaf_2      |  Peer-link     |
|Leaf_1_2    |vlanIf4090|  10.13.0.2/31                      |4090 | Leaf_2 po10 Leaf_2 po10        |  Peer-link     |
|Leaf_1_2    |eth7      |  None                             |903  |Leaf_2 po20  – po10 Client_2    |  Client        |
|Leaf_1_2    |loopback0 |  10.10.0.32/32                     | --  | None                           |  Loopback      |
|Leaf_1_2    |loopback1 |  10.11.0.32/32                     | --  | Shared with Leaf_2             |  Loopback      |
|----------|----------|-----------------------------------|-----|--------------------------------|----------------|
|Leaf_2_2    |eth1      |  10.12.0.3/31                      | --  |Leaf_2 eth1 – eth2 Spine_2      |  Interconnect  |
|Leaf_2_2    |eth2      |  10.12.1.3/31                      | --  |Leaf_2 eth2 – eth2 Spine_2      |  Interconnect  |
|Leaf_2_2    |eth3      |  10.13.0.1/31                      | --  |Leaf_2 eth3 – eth3  Leaf_2      |  Keepalive     |
|Leaf_2_2    |eth4      |  None                             |4090 |Leaf_2 po10 – po10  Leaf_2      |  Peer-link     |
|Leaf_2_2    |eth5      |  None                             |4090 |Leaf_2 po10 – po10  Leaf_2      |  Peer-link     |
|Leaf_2_2    |vlanIf4090|  10.13.0.3/31                      |4090 | Leaf_2 po10 Leaf_2 po10        |  Peer-link     |
|Leaf_2_2    |eth7      |  None                             |903  |Leaf_2 po20  – po10 Client_2    |  Client        |
|Leaf_2_2    |loopback0 |  10.10.0.33/32                     | --  | None                           |  Loopback      |
|Leaf_2_2    |loopback1 |  10.11.0.32/32                     | --  | Shared with Leaf_2             |  Loopback      |
|----------|----------|-----------------------------------|-----|--------------------------------|----------------|
|Leaf_3_2    |eth1      |  10.12.0.5/31                      | --  |Leaf_3 eth1 – eth1 Spine_2      |  Interconnect  |
|Leaf_3_2    |eth2      |  10.12.1.5/31                      | --  |Leaf_3 eth2 – eth1 Spine_2      |  Interconnect  |
|Leaf_3_2    |vlanIf903 |  172.16.0.1/24                    |903  | Virtual-IP                     |  VARP          |
|Leaf_3_2    |vlanIf905 |  172.16.2.1/24                    |905  | Virtual-IP                     |  VARP          |
|Leaf_3_2    |eth7      |  172.16.4.1/31                    | --  |Leaf_3 eth7  – eth1 Gateway-1   |  Client        |
|Leaf_3_2    |loopback0 |  10.10.0.34/32                     | --  | None                           |  Loopback      |
|Leaf_3_2    |loopback1 |  10.11.0.34/32                     | --  | Shared with Leaf_4             |  Loopback      |
|----------|----------|-----------------------------------|-----|--------------------------------|----------------|
|Spine_1_3   |eth1      |  10.22.0.0/31                      | --  |Spine_3 eth1 – eth1 Leaf_3      |  Interconnect  |
|Spine_1_3   |eth2      |  10.22.0.2/31                      | --  |Spine_3 eth2 – eth1 Leaf_3      |  Interconnect  |
|Spine_1_3   |eth3      |  10.22.0.4/31                      | --  |Spine_3 eth3 – eth1 Leaf_3      |  Interconnect  |
|Spine_1_3   |eth4      |  10.22.0.6/31                      | --  |Spine_3 eth4 – eth1 Leaf_4      |  Interconnect  |
|Spine_1_3   |loopback0 |  10.20.0.0/32                      | --  | None                           |  Loopback      |
|----------|----------|-----------------------------------|-----|--------------------------------|----------------|
|Leaf_1_3    |eth1      |  10.22.0.1/31                      | --  |Leaf_3 eth1 – eth1 Spine_3      |  Interconnect  |
|Leaf_1_3    |eth2      |  10.22.1.1/31                      | --  |Leaf_3 eth2 – eth1 Spine_3      |  Interconnect  |
|Leaf_1_3    |eth3      |  10.23.0.0/31                      | --  |Leaf_3 eth3 – eth3  Leaf_3      |  Keepalive     |
|Leaf_1_3    |eth4      |  None                             |4090 |Leaf_3 po10 – po10  Leaf_3      |  Peer-link     |
|Leaf_1_3    |eth5      |  None                             |4090 |Leaf_3 po10 – po10  Leaf_3      |  Peer-link     |
|Leaf_1_3    |vlanIf4090|  10.23.0.2/31                      |4090 | Leaf_3 po10 Leaf_3 po10        |  Peer-link     |
|Leaf_1_3    |eth7      |  None                             |903  |Leaf_3 po20  – po10 Client_3    |  Client        |
|Leaf_1_3    |loopback0 |  10.20.0.32/32                     | --  | None                           |  Loopback      |
|Leaf_1_3    |loopback1 |  10.21.0.32/32                     | --  | Shared with Leaf_3             |  Loopback      |
|----------|----------|-----------------------------------|-----|--------------------------------|----------------|
|Leaf_2_3    |eth1      |  10.22.0.3/31                      | --  |Leaf_3 eth1 – eth2 Spine_3      |  Interconnect  |
|Leaf_2_3    |eth2      |  10.22.1.3/31                      | --  |Leaf_3 eth2 – eth2 Spine_3      |  Interconnect  |
|Leaf_2_3    |eth3      |  10.23.0.1/31                      | --  |Leaf_3 eth3 – eth3  Leaf_3      |  Keepalive     |
|Leaf_2_3    |eth4      |  None                             |4090 |Leaf_3 po10 – po10  Leaf_3      |  Peer-link     |
|Leaf_2_3    |eth5      |  None                             |4090 |Leaf_3 po10 – po10  Leaf_3      |  Peer-link     |
|Leaf_2_3    |vlanIf4090|  10.23.0.3/31                      |4090 | Leaf_3 po10 Leaf_3 po10        |  Peer-link     |
|Leaf_2_3    |eth7      |  None                             |903  |Leaf_3 po20  – po10 Client_3    |  Client        |
|Leaf_2_3    |loopback0 |  10.20.0.33/32                     | --  | None                           |  Loopback      |
|Leaf_2_3    |loopback1 |  10.21.0.32/32                     | --  | Shared with Leaf_3             |  Loopback      |
|----------|----------|-----------------------------------|-----|--------------------------------|----------------|
|Leaf_3_3    |eth1      |  10.22.0.5/31                      | --  |Leaf_3 eth1 – eth1 Spine_3      |  Interconnect  |
|Leaf_3_3    |eth2      |  10.22.1.5/31                      | --  |Leaf_3 eth2 – eth1 Spine_3      |  Interconnect  |
|Leaf_3_3    |vlanIf903 |  172.16.0.1/24                    |903  | Virtual-IP                     |  VARP          |
|Leaf_3_3    |vlanIf905 |  172.16.2.1/24                    |905  | Virtual-IP                     |  VARP          |
|Leaf_3_3    |eth7      |  172.16.4.1/31                    | --  |Leaf_3 eth7  – eth1 Gateway-1   |  Client        |
|Leaf_3_3    |loopback0 |  10.20.0.34/32                     | --  | None                           |  Loopback      |
|Leaf_3_3    |loopback1 |  10.21.0.34/32                     | --  | Shared with Leaf_4             |  Loopback      |
|----------|----------|-----------------------------------|-----|--------------------------------|----------------|
|Client_1  |vlanIf903 |  172.16.0.10/24                   |903  |Client_1 po10 – po20  Leaf_1_2  |  Client        |
|Client_2  |vlanIf903 |  172.16.0.10/24                   |903  |Client_1 po10 – po20  Leaf_1_2  |  Client        |
|Client_3  |vlanIf903 |  172.16.2.20/24                   |905  |Client_3 po10 – po20  Leaf_5_6  |  Client        |
|----------|----------|-----------------------------------|-----|--------------------------------|----------------|
|DCI-1     |eth1      |  10.100.0.0/31                    | --  |Leaf_3 eth7  – eth1 Gateway-1   |  Interconnect  |
|DCI-1     |eth2      |  10.100.0.2/31                    | --  |Leaf_4 eth7  – eth2 Gateway-1   |  Interconnect  |
|DCI-1     |eth3      |  10.100.0.4/31                    | --  |Leaf_4 eth7  – eth2 Gateway-1   |  Interconnect  |
|DCI-1     |loopback0 |  8.8.8.8/32                       | --  | None                           |  Loopback      |
|DCI-1     |loopback1 |  1.1.1.1/32                       | --  | None                           |  Loopback      |


## Выполнение работ

В отличие от схем выше, мы будем использовать только 1 Spine на Site-2 и Site-3 так же, сократим количество лифов до 3х, и будем использовать только 1 DCI-свич

Интернет покажем в самом конце, от DCI свича - просто сделаем VRF, который будет его имитировать, чтобы не создавать еще одно устройство.

Схема сети тестового стенда, для наглядности, что происходит вообще, на ней показаны как старые интерконекты так и новые, старые подпишем.
Для старой выложим только итог, что она заработала, конфиги будут поделены на две части - до и после, до будут иметь подпись OLD, новые будут иметь подпись NEW.

Схема стенда

![](img/Schema.png)

#### Тут будет вся информация по старой сети, IP-планы, связность, состояние STP

### Строим сеть поверх старой, вся конфигурация будет отдельными файлами, показываем что получилось

Прикладываем скриншоты работы протоколов

Далее - длеаем переход по принципу - отключаем связность по l2 и l3 через старое решение и переводим его на новое - L2 цепляем к vxlan l3 погружаем в vrf Anycast gateway и разного рода Symmetric IRB, переводим клиентов с одного стыка на multihoming

по дороге показываем как переключали, проверяем связность.

### Ссылки!!!
