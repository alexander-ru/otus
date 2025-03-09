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
