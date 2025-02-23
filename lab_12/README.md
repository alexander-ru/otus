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

