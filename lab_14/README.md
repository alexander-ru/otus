# Лабораторная работа 14. IPsec over DMVPN
### Цели
1. Настроить GRE поверх IPSec между офисами Москва и С.-Петербург.
2. Настроить DMVPN поверх IPSec между Москва и Чокурдах, Лабытнанги.
### 1. Настроить GRE поверх IPSec между офисами Москва и С.-Петербург.
Сперва настроим R15:
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
Теперь настроим R18:
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
