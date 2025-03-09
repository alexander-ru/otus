# Лабораторная работа 13. VPN. GRE. DMVPN
### Цели
1. Настроить GRE между офисами Москва и С.-Петербург.
2. Настроить DMVMN между Москва и Чокурдах, Лабытнанги.
### 1. Настроить GRE между офисами Москва и С.-Петербург.
Настроим несколько GRE туннелей между Москвой и СПБ для достижения отказоустойчивости. Кроме того, реализуем схему OSPF over GRE:
```
R15#conf t
R15(config)#int tunnel 1
R15(config-if)#ip address 172.26.1.1 255.255.255.252
R15(config-if)#tunnel source e0/2
R15(config-if)#tunnel destination 90.90.91.2
R15(config-if)#ex
R15(config)#int tunnel 2
R15(config-if)#ip address 172.26.1.5 255.255.255.252
R15(config-if)#tunnel source e0/2
R15(config-if)#tunnel destination 90.90.91.6
R15(config-if)#ex
R15(config)#router ospf 1
R15(config-router)#network 172.26.1.0 0.0.0.3 area 50
R15(config-router)#network 172.26.1.4 0.0.0.3 area 50
```
```
R18#conf t
R18(config)#int tunnel 1
R18(config-if)#ip address 172.26.1.2 255.255.255.252
R18(config-if)#tunnel source e0/2
R18(config-if)#tunnel destination 150.150.150.157
R18(config-if)#ex
R18(config)#int tunnel 2
R18(config-if)#ip address 172.26.1.6 255.255.255.252
R18(config-if)#tunnel source e0/3
R18(config-if)#tunnel destination 150.150.150.157
R18(config-if)#ex
R18(config)#router ospf 1
R18(config-router)#network 172.26.1.0 0.0.0.3 area 50
R18(config-router)#network 172.26.1.4 0.0.0.3 area 50
R18(config-router)#redistribute eigrp 1 subnets
R18(config-router)#end
```
### 2. Настроить DMVMN между Москва и Чокурдах, Лабытнанги.
Настройка Hub роутера R15:
```
R15#conf t
R15(config)#interface Tunnel100
R15(config-if)#ip address 66.66.66.1 255.255.255.0
R15(config-if)#tunnel mode gre multipoint
R15(config-if)#tunnel source Ethernet0/2
R15(config-if)#ip nhrp map multicast dynamic
R15(config-if)#ip nhrp network-id 999
R15(config-if)#ip nhrp registration no-unique
R15(config-if)#ip nhrp redirect
R15(config-if)#no ip next-hop-self eigrp 100
R15(config-if)#no ip split-horizon eigrp 100
R15(config-if)#ex
R15(config)#route-map REDISTRIBUTE_to_DMVPN
R15(config-route-map)#match ip address 1
R15(config-route-map)#ex
R15(config)#router eigrp 100
R15(config-router)#network 66.66.66.0 0.0.0.255
R15(config-router)#redistribute ospf 1 metric 1500 10 255 1 1500 route-map REDISTRIBUTE_to_DMVPN
```
Настройка Spoke роутера R27:
```
R27#conf t
R27(config)#interface Tunnel100
R27(config-if)#ip address 66.66.66.27 255.255.255.0
R27(config-if)#tunnel mode gre multipoint
R27(config-if)#tunnel source Ethernet0/0
R27(config-if)#ip nhrp map 66.66.66.1 150.150.150.157
R27(config-if)#ip nhrp map multicast 150.150.150.157
R27(config-if)#ip nhrp nhs 66.66.66.1
R27(config-if)#ip nhrp network-id 999
R27(config-if)#ip nhrp shortcut
R27(config-if)#ex
R27(config)#router eigrp 100
R27(config-router)#network 10.40.2.0 0.0.0.255
R27(config-router)#network 66.66.66.0 0.0.0.255
```
Настройка Spoke роутера R28:
```
R28#conf t
R28(config)#interface Tunnel100
R28(config-if)#ip address 66.66.66.28 255.255.255.0
R28(config-if)#tunnel mode gre multipoint
R28(config-if)#tunnel source Ethernet0/0
R28(config-if)#ip nhrp map 66.66.66.1 150.150.150.157
R28(config-if)#ip nhrp map multicast 150.150.150.157
R28(config-if)#ip nhrp nhs 66.66.66.1
R28(config-if)#ip nhrp network-id 999
R28(config-if)#ip nhrp shortcut
R28(config-if)#ex
R28(config)#router eigrp 100
R28(config-router)#network 10.40.2.0 0.0.0.255
R28(config-router)#network 179.140.30.0 0.0.0.255
R28(config-router)#network 179.140.31.0 0.0.0.255
```
### Проверка
