# Лабораторная работа 6. OSPF. Фильтрация
### Цели
1. Маршрутизаторы R14-R15 находятся в зоне 0 - backbone.
2. Маршрутизаторы R12-R13 находятся в зоне 10. Дополнительно к маршрутам должны получать маршрут по умолчанию.
3. Маршрутизатор R19 находится в зоне 101 и получает только маршрут по умолчанию.
4. Маршрутизатор R20 находится в зоне 102 и получает все маршруты, кроме маршрутов до сетей зоны 101.
### Топология
![](osp.png)
### 1. Настройка Backbone
Как видно на рисунке выше между R14 и R15 нет прямого физического линка, что делает невозможным установлением соседства между этими роутерами и, следовательно Backbone зону они не образуют. Чтобы решить этот вопрос поднимем GRE туннель между ними через R12 и запустим процесс OSPF:
```
R14#conf t
R14(config)#interface Tunnel0
R14(config-if)#ip address 192.168.99.1 255.255.255.252
R14(config-if)#tunnel source 150.150.150.134
R14(config-if)#tunnel destination 150.150.150.146
R14(config-if)#ex
R14(config)#router ospf 1
R14(config-router)#router-id 14.14.14.14
R14(config-router)#network 192.168.99.0 0.0.0.3 area 0
```
```
R15#conf t
R15(config)#interface Tunnel0
R15(config-if)#ip address 192.168.99.2 255.255.255.252
R15(config-if)#tunnel source 150.150.150.146
R15(config-if)#tunnel destination 150.150.150.134
R15(config-if)#ex
R15(config)#router ospf 1
R15(config-router)#router-id 15.15.15.15
R15(config-router)#network 192.168.99.0 0.0.0.3 area 0
```
### 2. Базовая настройка OSPF на всех роутерах
```
R14#conf t
R14(config)#router ospf 1
R14(config-router)#network 150.150.150.128 0.0.0.3 area 101
R14(config-router)#network 150.150.150.132 0.0.0.3 area 10
R14(config-router)#network 150.150.150.148 0.0.0.3 area 10
```
```
R15#conf t
R15(config)#router ospf 1
R15(config-router)#network 150.150.150.136 0.0.0.3 area 102
R15(config-router)#network 150.150.150.140 0.0.0.3 area 10
R15(config-router)#network 150.150.150.144 0.0.0.3 area 10
```
```
R12#conf t
R12(config)#router ospf 1
R12(config-router)#router-id 12.12.12.12
R12(config-router)#network 150.150.150.0 0.0.0.63 area 10
R12(config-router)#network 150.150.150.64 0.0.0.63 area 10
R12(config-router)#network 150.150.150.132 0.0.0.3 area 10
R12(config-router)#network 150.150.150.144 0.0.0.3 area 10
```
```
R13#conf t
R13(config)#router ospf 1
R13(config-router)#router-id 13.13.13.13
R13(config-router)#network 150.150.150.0 0.0.0.63 area 10
R13(config-router)#network 150.150.150.64 0.0.0.63 area 10
R13(config-router)#network 150.150.150.140 0.0.0.3 area 10
R13(config-router)#network 150.150.150.148 0.0.0.3 area 10
```
```
R19#conf t
R19(config)#router ospf 1
R19(config-router)#router-id 19.19.19.19
R19(config-router)#network 150.150.150.128 0.0.0.3 area 101
```
```
R20#conf t
R20(config)#router ospf 1
R20(config-router)#router-id 20.20.20.20
R20(config-router)#network 150.150.150.136 0.0.0.3 area 102
```
