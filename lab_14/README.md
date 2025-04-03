# Лабораторная работа 14. IPsec over DMVPN
### Цели
1. Настроить GRE поверх IPSec между офисами Москва и С.-Петербург.
2. Настроить DMVPN поверх IPSec между Москва и Чокурдах, Лабытнанги.
### 1. Настроить GRE поверх IPSec между офисами Москва и С.-Петербург.
Сперва настроим IPsec на R15:
```
R15#conf t
R15(config)#crypto isakmp policy 10
R15(config-isakmp)#encryption aes 256
R15(config-isakmp)#authentication pre-share
R15(config-isakmp)#group 14
R15(config-isakmp)#ex
R15(config)#crypto isakmp key MY_KEY address 90.90.91.6
R15(config)#crypto ipsec transform-set SITE-TO_SITE esp-aes esp-sha-hmac
R15(cfg-crypto-trans)#ex
R15(config)#crypto ipsec profile SITE-TO-SITE-PROFILE
R15(ipsec-profile)#set transform-set SITE-TO_SITE
R15(ipsec-profile)#ex
R15(config)#interface tunnel 2
R15(config-if)#tunnel protection ipsec profile SITE-TO-SITE-PROFILE
```
Теперь настроим IPsec на R18:
```
R18#conf t
R18(config)#crypto isakmp policy 10
R18(config-isakmp)#encryption aes 256
R18(config-isakmp)#authentication pre-share
R18(config-isakmp)#group 14
R18(config-isakmp)#ex
R18(config)#crypto isakmp key MY_KEY address 150.150.150.157
R18(config)#crypto ipsec transform-set SITE-TO-SITE esp-aes esp-sha-hmac
R18(cfg-crypto-trans)#ex
R18(config)#crypto ipsec profile SITE-TO-SITE-PROFILE
R18(ipsec-profile)#set transform-set SITE-TO-SITE
R18(ipsec-profile)#ex
R18(config)#int tunnel 2
R18(config-if)#tunnel protection ipsec profile SITE-TO-SITE-PROFILE
```
Проверим IPsec туннель:  

![](1.png)
![](2.png)
![](3.png)
![](4.png)

### 2. Настроить DMVPN поверх IPSec между Москва и Чокурдах, Лабытнанги.
На R15, R27 и R28 делаем все то же самое, что и до этого:
```
R15#conf t
R15(config)#crypto isakmp policy 10
R15(config-isakmp)#encryption aes 256
R15(config-isakmp)#authentication pre-share
R15(config-isakmp)#group 14
R15(config-isakmp)#ex
R15(config)#crypto isakmp key DMVPN_KEY address 0.0.0.0 0.0.0.0
R15(config)#$c transform-set DMVPN_TRANSFORM esp-aes 256 esp-sha256-hmac
R15(cfg-crypto-trans)#mode transport
R15(cfg-crypto-trans)#ex
R15(config)#crypto ipsec profile DMVPN_PROFILE
R15(ipsec-profile)#set transform-set DMVPN_TRANSFORM
R15(ipsec-profile)#ex
R15(config)#int tunnel 100
R15(config-if)#tunnel protection ipsec profile DMVPN_PROFILE
R15(config-if)#end
```
```
R27#conf t
R27(config)#crypto isakmp policy 10
R27(config-isakmp)#encryption aes 256
R27(config-isakmp)#authentication pre-share
R27(config-isakmp)#group 14
R27(config-isakmp)#ex
R27(config)#crypto isakmp key DMVPN_KEY address 0.0.0.0 0.0.0.0
R27(config)#$c transform-set DMVPN_TRANSFORM esp-aes 256 esp-sha256-hmac
R27(cfg-crypto-trans)#mode transport
R27(cfg-crypto-trans)#ex
R27(config)#crypto ipsec profile DMVPN_PROFILE
R27(ipsec-profile)#set transform-set DMVPN_TRANSFORM
R27(ipsec-profile)#ex
R27(config)#int tunnel 100
R27(config-if)#tunnel protection ipsec profile DMVPN_PROFILE
```
```
R28#conf t
R28(config)#crypto isakmp policy 10
R28(config-isakmp)#encryption aes 256
R28(config-isakmp)#authentication pre-share
R28(config-isakmp)#group 14
R28(config-isakmp)#ex
R28(config)#crypto isakmp key DMVPN_KEY address 0.0.0.0 0.0.0.0
R28(config)#$c transform-set DMVPN_TRANSFORM esp-aes 256 esp-sha256-hmac
R28(cfg-crypto-trans)#mode transport
R28(cfg-crypto-trans)#ex
R28(config)#crypto ipsec profile DMVPN_PROFILE
R28(ipsec-profile)#set transform-set DMVPN_TRANSFORM
R28(ipsec-profile)#ex
R28(config)#int tunnel 100
R28(config-if)#tunnel protection ipsec profile DMVPN_PROFILE
```
Проверка:  

![](5.png)
![](6.png)
![](7.png)
![](8.png)
