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
S1(config-if)#ex
S1(config)#int e0/3
S1(config-if)#channel-group 1 mode desirable
```
```
S3>en
S3#conf t
S3(config)#int e0/0
S3(config-if)#channel-group 1 mode auto
S3(config-if)#ex
S3(config)#int e0/3
S3(config-if)#channel-group 1 mode auto
```
Включим порты e0/0 и e0/3 на S1 после настройки режимов PAgP:
```
S1(config-if)#no sh
```
Включим порты e0/0 и e0/3 на S3 после настройки режимов PAgP:
```
S3(config-if)#no sh
```
#### Шаг 2: Проверим конфигурации на портах

![](https://github.com/alexander-ru/otus/blob/main/lab_2.2%20(EtherChannel)/sh_channel-group-1_on_S1.png)

![](https://github.com/alexander-ru/otus/blob/main/lab_2.2%20(EtherChannel)/sh_switchport_on_S1.png)

Здесь запись в строке Operational Mode говорит о том, что порт e0/0 (как и e0/3) является участником группы портов Po1.
#### Шаг 3: Убедимся, что порты объединены

![](https://github.com/alexander-ru/otus/blob/main/lab_2.2%20(EtherChannel)/sh_etherchannel_summary_on_S3.png)

Аббревиатура SU означает, что Portchannel активен (U - use) и работает на L2 (S). Также в выводе отображаются порты, которые принадлежат первой группе портов.

Еще несколько полезных команд для диагностики:

![](https://github.com/alexander-ru/otus/blob/main/lab_2.2%20(EtherChannel)/sh_etherchannel_protocol_on_S3.png)

![](https://github.com/alexander-ru/otus/blob/main/lab_2.2%20(EtherChannel)/sh_etherchannel_load_balance.png)

#### Шаг 4: Настройте транковые порты
