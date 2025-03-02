# Лабораторная работа 12. Основные протоколы сети интернет
### Цели
1. Настроить NAT(PAT) на R14 и R15. Трансляция должна осуществляться в адрес автономной системы AS1001.
2. Настроить NAT(PAT) на R18. Трансляция должна осуществляться в пул из 5 адресов автономной системы AS2042.
3. Настроить статический NAT для R20.
4. Настроить NAT так, чтобы R19 был доступен с любого узла для удаленного управления.  
5*. Настроить статический NAT(PAT) для офиса Чокурдах.
6. Настроить для IPv4 DHCP сервер в офисе Москва на маршрутизаторах R12 и R13. VPC1 и VPC7 должны получать сетевые настройки по DHCP.
7. Настроить NTP сервер на R12 и R13. Все устройства в офисе Москва должны синхронизировать время с R12 и R13.
### 1. Настроить NAT(PAT) на R14 и R15. Трансляция должна осуществляться в адрес автономной системы AS1001.
```
R15#conf t
R15(config)#access-list 1 permit 150.150.150.0 0.0.0.63
R15(config)#access-list 1 permit 150.150.150.64 0.0.0.63
R15(config)#ip nat inside source list 1 interface e0/2 overload
R15(config)#int e0/0
R15(config-if)#ip nat inside
R15(config-if)#ex
R15(config)#int e0/1
R15(config-if)#ip nat inside
R15(config-if)#ex
R15(config)#int e0/2
R15(config-if)#ip nat outside
```
```
R14#conf t
R14(config)#access-list 1 permit 150.150.150.0 0.0.0.63
R14(config)#access-list 1 permit 150.150.150.64 0.0.0.63
R14(config)#ip nat inside source list 1 interface e0/2 overload
R14(config)#int e0/0
R14(config-if)#ip nat inside
R14(config-if)#ex
R14(config)#int e0/1
R14(config-if)#ip nat inside
R14(config-if)#ex
R14(config)#int e0/2
R14(config-if)#ip nat outside
```
### 2. Настроить NAT(PAT) на R18. Трансляция должна осуществляться в пул из 5 адресов автономной системы AS2042.
На R18 создадим Loopback интерфейс с IP-адресом из подсети 90.90.91.20/29, пять адресов из которой будут использоваться для NAT (PAT):
```
R18>en
R18#conf t
R18(config)#int loopback 1
R18(config-if)#ip address 90.90.92.1 255.255.255.240
```
```
R18(config)#access-list 1 permit 90.90.90.0 0.0.0.127
R18(config)#access-list 1 permit 90.90.90.128 0.0.0.127
R18(config)#ip nat pool NAT_POOL 90.90.92.0 90.90.92.5 netmask 255.255.255.240
R18(config)#ip nat inside source list 1 pool NAT_POOL overload
R18(config)#int e0/2
R18(config-if)#ip nat outside
R18(config-if)#ex
R18(config)#int e0/3
R18(config-if)#ip nat outside
R18(config-if)#ex
R18(config)#int e0/1
R18(config-if)#ip nat inside
R18(config-if)#ex
R18(config)#int e0/0
R18(config-if)#ip nat inside
R18(config-if)#ex
```
Начнем анонсировать внешние адреса, с которых пользователи локальной сети будут выходить во внешний мир:
```
R18(config)#router bgp 2042
R18(config-router)#network 90.90.92.0 mask 255.255.255.240
R18(config-router)#ex
```
Добавим PAT адреса в список разрешенных, которые будут анонсироваться в AS 520:
```
R18(config)#ip prefix-list DENY_UPDATE_to_AS520 seq 20 permit 90.90.92.0/28
R18(config)#end
R18#clear ip bgp * soft
```
### 3. Настроить статический NAT для R20.
```
R15(config)#ip nat inside source static 150.150.150.138 150.150.151.2
R15(config)#int e0/3
R15(config-if)#ip nat inside
```
```
R14(config)#ip nat inside source static 150.150.150.138 150.150.152.2
```
### 4. Настроить NAT так, чтобы R19 был доступен с любого узла для удаленного управления.
```
R15(config)#ip nat inside source static tcp 150.150.150.129 23 150.150.151.3 23
```
```
R14(config)#ip nat inside source static tcp 150.150.150.129 23 150.150.152.3 23
R14(config)#int e0/3
R14(config-if)#ip nat inside
```
### 5*. Настроить статический NAT(PAT) для офиса Чокурдах.
```
R28#conf t
R28(config)#access-list 1 permit 179.140.30.0 0.0.0.255
R28(config)#access-list 1 permit 179.140.31.0 0.0.0.255
R28(config)#ip nat inside source list 1 interface e0/1 overload
R28(config)#ip nat inside source list 1 interface e0/0 overload
R28(config)#int e0/2.30
R28(config-subif)#ip nat inside
R28(config-subif)#ex
R28(config)#int e0/2.31
R28(config-subif)#ip nat inside
R28(config-subif)#ex
R28(config)#int e0/1
R28(config-if)#ip nat outside
R28(config-if)#ex
R28(config)#int e0/0
R28(config-if)#ip nat outside
```
### 6. Настроить для IPv4 DHCP сервер в офисе Москва на маршрутизаторах R12 и R13. VPC1 и VPC7 должны получать сетевые настройки по DHCP.
```
R12#conf t
R12(config)#ip dhcp pool VLAN_10
R12(dhcp-config)#network 150.150.150.0 /26
R12(dhcp-config)#default-router 150.150.150.62
R12(dhcp-config)#dns-server 77.88.8.8
R12(dhcp-config)#ex
R12(config)#ip dhcp pool VLAN_20
R12(dhcp-config)#network 150.150.150.64 /26
R12(dhcp-config)#default-router 150.150.150.126
R12(dhcp-config)#dns-server 77.88.8.8
R12(dhcp-config)#ex
R12(config)#ip dhcp excluded-address 150.150.150.32 150.150.150.62
R12(config)#ip dhcp excluded-address 150.150.150.96 150.150.150.126
```
```
R13#conf t
R13(config)#ip dhcp pool VLAN_10
R13(dhcp-config)#network 150.150.150.0 /26
R13(dhcp-config)#default-router 150.150.150.62
R13(dhcp-config)#dns-server 77.88.8.8
R13(dhcp-config)#ex
R13(config)#ip dhcp pool VLAN_20
R13(dhcp-config)#network 150.150.150.64 /26
R13(dhcp-config)#default-router 150.150.150.126
R13(dhcp-config)#dns-server 77.88.8.8
R13(dhcp-config)#ex
R13(config)#ip dhcp excluded-address 150.150.150.1 150.150.150.31
R13(config)#ip dhcp excluded-address 150.150.150.65 150.150.150.95
```
