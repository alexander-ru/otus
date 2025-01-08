# Лабораторная работа 7. IS-IS.
### Цели
1. Настроите IS-IS в ISP Триада.
2. R23 и R25 находятся в зоне 2222.
3. R24 находится в зоне 24.
4. R26 находится в зоне 26.
### 1. Настройка R23
Для работы IS-IS достаточно базово включить протокол IS-IS, дать NET-адрес и включить IS-IS на интерфейсах:
```
R23#conf t
R23(config)#router isis
R23(config-router)#net 49.2222.0000.0000.0023.00
R23(config-router)#ex
R23(config)#int range e0/1-2
R23(config-if-range)#ip router isis
```
### 2. Настройка R25
```
R25#conf t
R25(config)#router isis
R25(config-router)#net 49.2222.0000.0000.0025.00
R25(config-router)#ex
R25(config)#int e0/0
R25(config-if)#ip router isis
R25(config-if)#ex
R25(config)#int e0/2
R25(config-if)#ip router isis
```
### 3. Настройка R24
```
R24#conf t
R24(config)#router isis
R24(config-router)#net 49.24.0000.0000.0024.00
R24(config-router)#ex
R24(config)#int range e0/1-2
R24(config-if-range)#ip router isis
R24(config-if-range)#end
```
### 4. Настройка R26
```
R26#conf t
R26(config)#router isis
R26(config-router)#net 49.26.0000.0000.0026.00
R26(config-router)#ex
R26(config)#int e0/0
R26(config-if)#ip router isis
R26(config-if)#ex
R26(config)#int e0/2
R26(config-if)#ip router isis
```
### 5. Проверка IS-IS
Таблица соседей:

![](neighbors_table)

Таблица маршрутизации:

![](routing_table)
