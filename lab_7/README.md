# Лабораторная работа 7. IS-IS.
### Цели
1. Настроите IS-IS в ISP Триада.
2. R23 и R25 находятся в зоне 2222.
3. R24 находится в зоне 24.
4. R26 находится в зоне 26.
### 1. Настройка R23
```
R23#conf t
R23(config)#router isis
R23(config-router)#net 49.2222.0000.0000.0023.00
R23(config-router)#ex
R23(config)#int range e0/1-2
R23(config-if-range)#ip router isis
```
