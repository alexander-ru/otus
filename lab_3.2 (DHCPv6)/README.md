# Лабраторная работа 3. Настройка DHCPv6 
### Топология
![](1.png)
### Таблица адресации

|Устройство|Интерфейс |IPv6-адрес            |
|----------|----------|----------------------|
| R1			 | e0/0		  |2001:db8:acad:2።1/64  |
|          |          |fe80::1               |
|          | e0/1     |2001:db8:acad:1።1/64  |
|          |          |fe80::1               |
| R2       | e0/0     |2001:db8:acad:2።2/64  |
|          |          |fe80::2               |
|          | e0/1     |2001:db8:acad:3።1/64  |
|          |          |fe80::1               |
| PC-A     | NIC      |DHCP                  |
| PC-B     | NIC      |DHCP                  |

### Задачи
1. Создание сети и настройка основных параметров устройства 
2. Проверка назначения адреса SLAAC от R1 
3. Настройка и проверка сервера DHCPv6 без отслеживания состояния на R1 
4. Настройка и проверка сервера DHCPv6 с отслеживанием состояния на R1 
5. Настройка и проверка DHCPv6 Relay на R2

### Часть 1. Создание сети и настройка основных параметров устройства
#### Шаг 1. Создайте сеть согласно топологии. 
#### Шаг 2. Настройте базовые параметры каждого коммутатора (необязательно)
#### Шаг 3. Произведите базовую настройку маршрутизаторов. 
```
Router>en
Router#conf t
Router(config)#ipv6 unicast-routing
Router(config)#no ip domain lookup
Router(config)#line console 0
Router(config-line)#logging synchronous
Router(config-line)#exit
Router(config)#host R1
R1(config)#do wr
```
```
Router#
Router#conf t
Router(config)#ipv6 unicast-routing
Router(config)#no ip domain lookup
Router(config)#line console 0
Router(config-line)#logging synchronous
Router(config-line)#exit
Router(config)#host R2
R2(config)#do wr
```
#### Шаг 4. Настройка интерфейсов и маршрутизации для обоих маршрутизаторов. 
##### a. Настройте интерфейсы e0/0 и e0/1 на R1 и R2 с адресами IPv6, указанными в таблице выше. 
```
R1#
R1#conf t
R1(config)#int e0/1
R1(config-if)#ipv6 address 2001:db8:acad:1::1/64
R1(config-if)#no sh
R1(config-if)#ipv6 address fe80::1 link-local
R1(config-if)#ex
R1(config)#int e0/0
R1(config-if)#ipv6 address 2001:db8:acad:2::1/64
R1(config-if)#no sh
R1(config-if)#ipv6 address fe80::1 link-local
R1(config-if)#do wr
```
```
R2>en
R2#
R2#conf t
R2(config)#int e0/0
R2(config-if)#ipv6 address 2001:db8:acad:2::2/64
R2(config-if)#no sh
R2(config-if)#ipv6 address fe80::2 link-local
R2(config-if)#ex
R2(config)#int e0/1
R2(config-if)#ipv6 address 2001:db8:acad:3::1/64
R2(config-if)#no sh
R2(config-if)#ipv6 address fe80::1 link-local
R2(config-if)#do wr
```
##### b. Настройте маршрут по умолчанию на каждом маршрутизаторе, который указывает на IP-адрес e0/0 на другом маршрутизаторе. 
```
R1#
R1#conf t
R1(config)#ipv6 route ::/0 2001:db8:acad:2::2
```
```
R2#
R2#conf t
R2(config)#ipv6 route ::/0 2001:db8:acad:2::1
```
##### c. Убедитесь, что маршрутизация работает с помощью пинга адреса e0/1 R2 из R1 
![](2.png)
##### d. Сохраните текущую конфигурацию в файл загрузочной конфигурации. 
```
R1#wr
```
```
R2#wr
```
### Часть 2. Проверка назначения адреса SLAAC от R1
После ввода команды ```ipv6 unicast routing``` на маршрутизаторе по умолчанию назначение GUA (Global Unique Address) осуществляется способом SLAAC. Этим способом устройство посредством сообщения от роутера ICMPv6 Router Advertisement (RA) получает от него префикс и длину префикса сети. Далее устройство будет генерировать оставшуюся 64-битную часть IPv6 адреса через алгоритм EUI-64 или случайным образом. Посмотрим на сообщение RA:

![](5.png)

Теперь проверим, что PC-A получил префикс от роутера и сгенерировал себе вторую часть адреса:

![](3.png)

### Часть 3. Настройка и проверка сервера DHCPv6 на R1
#### Шаг 1. Более подробно изучите конфигурацию PC-A. 
##### a. Выполните команду ipconfig /all на PC-A и посмотрите на результат. 
