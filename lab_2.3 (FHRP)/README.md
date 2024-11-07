# Лабораторная работа. Настройка протоколов HSRP и GLBP
### Топология
![](1.png)
### Таблица адресации
|Устройство|Интерфейс|IP-адрес       |Маска подсети   |Шлюз по умолчанию|
|----------|---------|---------------|----------------|-----------------|
| R1			 | e0/1		 |192.168.1.1    |255.255.255.0   |-                |
|          | e0/0    |10.1.1.1       |255.255.255.252 |-                |
| R2       | e0/0    |10.1.1.2       |255.255.255.252 |-                |
|          | e0/1    |10.2.2.2       |255.255.255.252 |-                |
|          | Lo0     |209.165.200.225|255.255.255.224 |-                |
| R3       | e0/1    |10.2.2.1       |255.255.255.252 |-                |
|          | e0/2    |192.168.1.3    |255.255.255.0   |-                |
| S1       | VLAN 1  |192.168.1.11   |255.255.255.0   |192.168.1.1      |
| S2       | VLAN 1  |192.168.1.13   |255.255.255.0   |192.168.1.3      |
| PC-A     | NIC     |192.168.1.31   |255.255.255.0   |192.168.1.1      |
| PC-B     | NIC     |192.168.1.33   |255.255.255.0   |192.168.1.3      |

### Задачи
Часть 1. Построение сети и проверка соединения
Часть 2. Настройка обеспечения избыточности на первом хопе с помощью HSRP
Часть 3. Настройка обеспечения избыточности на первом хопе с помощью GLBP

### Часть 1: Построение сети и проверка соединения
#### Шаг 1: Подключите кабели в сети в соответствии с топологией.
#### Шаг 2: Настройте узлы ПК.
```
VPCS> ip 192.168.1.31 255.255.255.0 192.168.1.1
VPCS> save
```
```
VPCS : ip 192.168.1.33 255.255.255.0 192.168.1.3
VPCS> save
```
```
#### Шаг 3: Выполните инициализацию и перезагрузку маршрутизатора и коммутаторов.
#### Шаг 4: Настройте базовые параметры каждого маршрутизатора.
Router#conf t
Router(config)#no ip domain lookup
Router(config)#line console 0
Router(config-line)#logging synchronous
Router(config-line)#exit
Router(config)#host R1
R1(config)#int e0/1
R1(config-if)#ip address 192.168.1.1 255.255.255.0
R1(config-if)#no sh
R1(config-if)#ex
R1(config)#int e0/0
R1(config-if)#ip address 10.1.1.1 255.255.255.252
R1(config-if)#no sh
R1#wr
```
```
Router>en
Router#conf t
Router(config)#no ip domain lookup
Router(config)#line console 0
Router(config-line)#logging synchronous
Router(config-line)#exit
Router(config)#host R2
R2(config)#int e0/0
R2(config-if)#ip address 10.1.1.2 255.255.255.252
R2(config-if)#no sh
R2(config-if)#exit
R2(config)#int e0/1
R2(config-if)#ip address 10.2.2.2 255.255.255.252
R2(config-if)#no sh
R2(config)#int l0
R2(config-if)#ip address 209.165.200.225 255.255.255.224
R2(config-if)#no sh
R2(config-if)#end
R2#wr
```
```
Router>en
Router#
Router#conf t
Router(config)#no ip domain lookup
Router(config)#line console 0
Router(config-line)#logging synchronous
Router(config-line)#exit
Router(config)#int e0/1
Router(config-if)#ip address 10.2.2.1 255.255.255.252
Router(config-if)#no sh
Router(config-if)#ex
Router(config)#int e0/2
Router(config-if)#ip address 192.168.1.3 255.255.255.0
Router(config-if)#no sh
Router(config-if)#ex
Router(config)#Host R3
R3(config)#do wr
```
