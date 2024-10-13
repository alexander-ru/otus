# Лабораторная работа 2. Развертывание коммутируемой сети с резервными каналами
### Топология

### Таблица адресации
Устройство  | Интерфейс | IP-адрес | Маска подсети
------------|-----------|----------|--------------
S1  | VLAN 1 | 192.168.1.1 | 255.255.255.0
S2  | VLAN 1 | 192.168.1.2 | 255.255.255.0
S3  | VLAN 1 | 192.168.1.3 | 255.255.255.0
### Цели
1. Создание сети и настройка основных параметров устройства
2. Выбор корневого моста
3. Наблюдение за процессом выбора протоколом STP порта, исходя из стоимости портов
4. Наблюдение за процессом выбора протоколом STP порта, исходя из приоритета портов
### 1. Создание сети и настройка основных параметров устройства
Базово настроим основные параметры на S1 и на остальных коммутаторах по аналогии:
```
Switch>en
Switch#conf t
Switch(config)#no ip domain lookup
Switch(config)#hostname S1
S1(config)#banner motd "ATTENTION"
S1(config)#line console 0
S1(config-line)#logging synchronous
S1(config-line)#ex
S1(config)#int vlan 1
S1(config-if)#ip address 192.168.1.1 255.255.255.0
S1(config-if)#no sh
S1(config-if)#do wr
```
<s>(Т.к. в прошлой ЛР уже настраивалась парольная защита, то в этой ЛР я ее решил опустить для упрощения управления устройствами)</s>

Проверим связность от S1 до S2 и S3:

![](https://github.com/alexander-ru/otus/blob/main/lab_2/ping_from_s1_to_s2-s3.png)

Теперь от S2 до S3:

![](https://github.com/alexander-ru/otus/blob/main/lab_2/ping_s2-s3.png)

### 2. Выбор корневого моста
#### Шаг 1. Отключим все порты на коммутаторах:
```
S1#conf t
S1(config)#int range e0/0-3
S1(config-if-range)#sh
```
```
S2#conf t
S2(config)#int range e0/0-3
S2(config-if-range)#sh
```
```
S3#conf t
S3(config)#int range e0/0-3
S3(config-if-range)#sh
```
#### Шаг 2. Настроим подключенные порты как транковые
```
S1(config)#int range e0/0-3
S1(config-if-range)#switchport trunk encapsulation dot1q
S1(config-if-range)#switchport mode trunk
```
```
S2(config)#int range e0/0-3
S2(config-if-range)#switchport trunk encapsulation dot1q
S2(config-if-range)#switchport mode trunk
```
```
S3(config)#int range e0/0-3
S3(config-if-range)#switchport trunk encapsulation dot1q
S3(config-if-range)#switchport mode trunk
```
