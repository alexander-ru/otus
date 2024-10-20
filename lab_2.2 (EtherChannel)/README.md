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
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#host S1
S1(config)#no ip d
S1(config)#no ip d
S1(config)#no ip do
S1(config)#no ip domain loo
S1(config)#no ip domain lookup
S1(config)#logg
S1(config)#logging sy
S1(config)#logging syn
S1(config)#logging syn?
Hostname or A.B.C.D

S1(config)#logging ?
  Hostname or A.B.C.D  IP address of the logging host
  buffered             Set buffered logging parameters
  buginf               Enable buginf logging for debugging
  cns-events           Set CNS Event logging level
  console              Set console logging parameters
  count                Count every log message and timestamp last occurance
  delimiter            Append delimiter to syslog messages
  discriminator        Create or modify a message discriminator
  exception            Limit size of exception flush output
  facility             Facility parameter for syslog messages
  history              Configure syslog history table
  host                 Set syslog server IP address and parameters
  message-counter      Configure log message to include certain counter value
  monitor              Set terminal line (monitor) logging parameters
  on                   Enable logging to all enabled destinations
  origin-id            Add origin ID to syslog messages
  persistent           Set persistent logging parameters
  queue-limit          Set logger message queue size
  rate-limit           Set messages per second limit
  reload               Set reload logging level
  server-arp           Enable sending ARP requests for syslog servers when
                       first configured
  snmp-trap            Set syslog level for sending snmp trap
  source-interface     Specify interface for source address in logging
                       transactions
  trap                 Set syslog server logging level
  userinfo             Enable logging of user info on privileged mode enabling

S1(config)#logging
% Incomplete command.

S1(config)#
S1(config)#logging syn
S1(config)#logging syn?
Hostname or A.B.C.D

S1(config)#ex
% Ambiguous command:  "ex"
S1(config)#exit
S1#logg
S1#logging
*Oct 20 11:19:42.156: %SYS-5-CONFIG_I: Configured from console by console
S1#logging sy
S1#logging syn
S1#logging syn?
% Unrecognized command
S1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
S1(config)#lim
S1(config)#lin
S1(config)#line co
S1(config)#line console 0
S1(config-line)#logg
S1(config-line)#logging sy
S1(config-line)#logging synchronous
S1(config-line)#
S1(config-line)#
S1(config-line)#ex
% Ambiguous command:  "ex"
S1(config-line)#exit
S1(config)#int r
S1(config)#int range e0/0-3
S1(config-if-range)#sh
S1(config-if-range)#shutdown
S1(config-if-range)#
*Oct 20 11:21:04.009: %LINK-5-CHANGED: Interface Ethernet0/0, changed state to administratively down
*Oct 20 11:21:04.009: %LINK-5-CHANGED: Interface Ethernet0/1, changed state to administratively down
*Oct 20 11:21:04.009: %LINK-5-CHANGED: Interface Ethernet0/2, changed state to administratively down
*Oct 20 11:21:04.009: %LINK-5-CHANGED: Interface Ethernet0/3, changed state to administratively down
*Oct 20 11:21:05.009: %LINEPROTO-5-UPDOWN: Line protocol on Interface Ethernet0/0, changed state to down
*Oct 20 11:21:05.009: %LINEPROTO-5-UPDOWN: Line protocol on Interface Ethernet0/1, changed state to down
*Oct 20 11:21:05.009: %LINEPROTO-5-UPDOWN: Line protocol on Interface Ethernet0/2, changed state to down
*Oct 20 11:21:05.009: %LINEPROTO-5-UPDOWN: Line protocol on Interface Ethernet0/3, changed state to down
S1(config-if-range)#
S1(config-if-range)#
S1(config-if-range)#end
S1#
S1#sh ip in
*Oct 20 11:21:56.386: %SYS-5-CONFIG_I: Configured from console by console
S1#sh ip int br
Interface              IP-Address      OK? Method Status                Protocol
Ethernet0/0            unassigned      YES unset  administratively down down
Ethernet0/1            unassigned      YES unset  administratively down down
Ethernet0/2            unassigned      YES unset  administratively down down
Ethernet0/3            unassigned      YES unset  administratively down down
Ethernet1/0            unassigned      YES unset  up                    up
Ethernet1/1            unassigned      YES unset  up                    up
Ethernet1/2            unassigned      YES unset  up                    up
Ethernet1/3            unassigned      YES unset  up                    up
S1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
S1(config)#int g
S1(config)#int ga
S1(config)#int r
S1(config)#int range e1/1-3
S1(config-if-range)#sh
S1(config-if-range)#shutdown
S1(config-if-range)#
*Oct 20 11:22:25.131: %LINK-5-CHANGED: Interface Ethernet1/1, changed state to administratively down
*Oct 20 11:22:25.131: %LINK-5-CHANGED: Interface Ethernet1/2, changed state to administratively down
*Oct 20 11:22:25.131: %LINK-5-CHANGED: Interface Ethernet1/3, changed state to administratively down
*Oct 20 11:22:26.136: %LINEPROTO-5-UPDOWN: Line protocol on Interface Ethernet1/1, changed state to down
*Oct 20 11:22:26.136: %LINEPROTO-5-UPDOWN: Line protocol on Interface Ethernet1/2, changed state to down
*Oct 20 11:22:26.136: %LINEPROTO-5-UPDOWN: Line protocol on Interface Ethernet1/3, changed state to down
S1(config-if-range)#exit
S1(config)#
S1(config)#
S1(config)#
S1(config)#vl
S1(config)#vlan 99
S1(config-vlan)#n
S1(config-vlan)#na
S1(config-vlan)#name Management
S1(config-vlan)#ex
S1(config)#vl
S1(config)#vlan 10
S1(config-vlan)#na
S1(config-vlan)#name Staff
S1(config-vlan)#ex
S1(config)#int e1/0
S1(config-if)#sw
S1(config-if)#switchport m
S1(config-if)#switchport mode ac
S1(config-if)#switchport mode access
S1(config-if)#sw
S1(config-if)#switchport ac
S1(config-if)#switchport access v
S1(config-if)#switchport access vlan 10
S1(config-if)#ex
S1(config)#int vl
S1(config)#int vlan 99
S1(config-if)#ip add
S1(config-if)#ip address
*Oct 20 11:24:16.754: %LINEPROTO-5-UPDOWN: Line protocol on Interface Vlan99, changed state to down
S1(config-if)#ip address 192.168.99.11 255.255.255.0
S1(config-if)#no sh
S1(config-if)#wr
              ^
% Invalid input detected at '^' marker.

S1(config-if)#
*Oct 20 11:24:33.604: %LINK-3-UPDOWN: Interface Vlan99, changed state to down
S1(config-if)#do wr
Building configuration...
Compressed configuration from 1001 bytes to 653 bytes[OK]
S1(config-if)#
```
