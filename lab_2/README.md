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
#### Шаг 3:	Включим порты E0/2 и E0/1 на всех коммутаторах
```
S1(config)#int range e0/1-2
S1(config-if-range)#no sh
```
```
S2(config)#int range e0/1-2
S2(config-if-range)#no sh
```
```
S3(config)#int range e0/1-2
S3(config-if-range)#no sh
```
#### Шаг 4:	Отобразите данные протокола spanning-tree
![](https://github.com/alexander-ru/otus/blob/main/lab_2/show_stp_for_s1.png)

![](https://github.com/alexander-ru/otus/blob/main/lab_2/show_stp_for_s2.png)

![](https://github.com/alexander-ru/otus/blob/main/lab_2/show_stp_for_s3.png)

Исходя из вывода команды видно, что Root Bridge стал коммутатор S2. Так как приоритет у всех одинаковый (32 769), то Root Bridge выбирается по самому меньшему значению MAC адреса коммутатора из всех трех. А самый меньший у S2 -  aabb.cc00.e000.

После того как Root Bridge был определен, его порты автоматически становятся Designated. Далее происходит выбор корневых портов на коммутаторах S1 и S3 по критерию наименьшего Root Path Cost: коммутаторы S1 и S3 в качестве Root портов выбирают порты E0/2, т.к. их стоимость самая минимальная (Cost = 100). 

Выбрать Designated порты коммутаторам S1 и S3 по критерию Root Path Cost не выйдет, т.к. они у обоих одинаковы. А вот по наименьшему значению Bridge ID выбирается Designated порт E0/1 коммутатора S1. Порт E0/1 коммутатора S3 соотвественно будут Alternate. Ниже зарисовал итоговую схему сходимости:

![](https://github.com/alexander-ru/otus/blob/main/lab_2/stp_topology_converged.png)

### 3. Наблюдение за процессом выбора протоколом STP порта, исходя из стоимости портов
#### Шаг 1:	Определите коммутатор с заблокированным портом
Как уже выяснили это коммутатор S3, порт E0/1.
#### Шаг 2:	Измените стоимость порта
Изменим стоимость Root порта E0/2 коммутатора S3 на 99 (по умолчанию для Ethernet cost = 100):
```
S3(config)#int e0/2
S3(config-if)#spanning-tree cost 99
```
После этого коммутаторы пересчитают топологию и ранее заблокированный порт E0/1 коммутатора S3 станет Designated, а ранее Designated порт E0/1 коммутатора S1 станет Alternate. Все потому, что теперь общая стоимость двух портов до Root коммутатора на коммутаторе S3 равна 199, а для S1 - 200:

![](https://github.com/alexander-ru/otus/blob/main/lab_2/s3_change_cost.png)
