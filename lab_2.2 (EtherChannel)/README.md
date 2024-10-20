# Лабораторная работа 2.2. Настройка EtherChannel
### Топология

![](https://github.com/alexander-ru/otus/blob/main/lab_2.2%20(EtherChannel)/topology.png)

### Таблица адресации
Устройство    | Интерфейс     | IP-адрес        | Маска подсети
------------- | ------------- | ----------------| ------------------
S1            | VLAN 99       | 192.168.99.11   | 255.255.255.0
S2            | VLAN 99       | 192.168.99.12   | 255.255.255.0
S3            | VLAN 99       | 192.168.99.13   | 255.255.255.0
PC-A          | NIC           | 192.168.10.1    | 255.255.255.0
PC-B          | NIC           | 192.168.10.2    | 255.255.255.0
PC-C          | NIC           | 192.168.10.3    | 255.255.255.0

### Задачи
1. Настройка базовых параметров коммутатора
2. Настройка PAgP
3. Настройка LACP
### Часть 1: Настройка основных параметров коммутатора
Настроим коммутатор S1 и остальные коммутаторы по аналогии:
```
Switch>en
Switch#conf t
Switch(config)#host S1
S1(config)#no ip domain lookup
S1(config)#line console 0
S1(config-line)#logging synchronous
S1(config-line)#exit
S1(config)#int range e0/0-3
S1(config-if-range)#sh
S1(config-if-range)#exit
S1(config)#int range e1/1-3
S1(config-if-range)#sh
S1(config-if-range)#exit
S1(config)#vlan 99
S1(config-vlan)#name Management
S1(config-vlan)#ex
S1(config)#vlan 10
S1(config-vlan)#name Staff
S1(config-vlan)#ex
S1(config)#int e1/0
S1(config-if)#switchport mode access
S1(config-if)#switchport access vlan 10
S1(config-if)#ex
S1(config)#int vlan 99
S1(config-if)#ip address 192.168.99.11 255.255.255.0
S1(config-if)#no sh
S1(config-if)#do wr
Building configuration...
Compressed configuration from 1001 bytes to 653 bytes[OK]
S1(config-if)#
```
### Часть 2: Настройка PAgP
#### Шаг 1: Настройте PAgP на S1 и S3
Все доступные режимы для настройки EtherChannel:

![](https://github.com/alexander-ru/otus/blob/main/lab_2.2%20(EtherChannel)/etherchannel_mode.png)

Для образования канала между S1 и S3 настроим порты на S1 с использованием рекомендуемого режима (desirable), а порты на S3 — с использованием автоматического режима (auto):
```
S1>en
S1#conf t
S1(config)#int e0/0
S1(config-if)#channel-group 1 mode desirable
Creating a port-channel interface Port-channel 1

S1(config-if)#ex
S1(config)#int e0/3
S1(config-if)#channel-group 1 mode desirable

*Oct 20 11:51:46.031: %LINK-3-UPDOWN: Interface Ethernet0/3, changed state to up
*Oct 20 11:51:47.036: %LINEPROTO-5-UPDOWN: Line protocol on Interface Ethernet0/3, changed state to up
```
Включим порты e0/0 и e0/3 на S1 после настройки режимов PAgP:
```
S1(config-if)#no sh
```
Включим порты e0/0 и e0/3 на S3 после настройки режимов PAgP:
```
S3(config-if)#no sh
```
