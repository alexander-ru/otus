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
Настроим коммутатор S1:
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
