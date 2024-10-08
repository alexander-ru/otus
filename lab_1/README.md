# Лабораторная работа № 1. Маршрутизация между VLAN
### Топология
![](https://github.com/alexander-ru/otus/blob/main/lab_1/topology.png)
### Таблица адресации
Устройство  | Интерфейс | IP-адрес | Маска подсети | Шлюз по умолчанию
------------|-----------|----------|---------------|------------------
R1  | E0/0.3 | 192.168.3.1 | 255.255.255.0 | ----
|   | E0/0.4 | 192.168.4.1 | 255.255.255.0 | ----
|   |E0/0.8  | ---- |---- | ----
S1  | VLAN 3 | 192.168.3.11 | 255.255.255.0 | 192.168.3.1
S2  | VLAN 3 | 192.168.3.12 | 255.255.255.0 | 192.168.3.1
PC-A | eth0 | 192.168.3.3 | 255.255.255.0 | 192.168.3.1
PC-B | eth0 | 192.168.4.3 | 255.255.255.0 | 192.168.4.1
### Таблица VLAN
VLAN | Имя | Назначенный интерфейс
---- | ---- | ---- 
3 | Management | S1: VLAN 3 
| | | S2: VLAN 3
| | | S1: E0/2
4 | Operations | S2: E0/1
7 | ParkingLot | S1: E0/3 
| | | S2: E0/2, E0/3 
8 | Native | ----
### Цели работы
1. Создание сети и настройка основных параметров устройства
2. Создание VLAN и назначение портов коммутатора
3. Настройка канала 802.1Q между коммутаторами
4. Настройка маршрутизации между VLAN на маршрутизаторе
5. Проверка работы маршрутизации между VLAN

### 1. Создание сети и настройка основных параметров устройства
Для начала отключим поиск DNS и настроим пароли для входа в систему:

![](https://github.com/alexander-ru/otus/blob/main/lab_1/router_settings_1.png)
![](https://github.com/alexander-ru/otus/blob/main/lab_1/router_settings_2.png)

Включим шифрование паролей, настроим время и создадим баннер с предупреждением:

![](https://github.com/alexander-ru/otus/blob/main/lab_1/router_settings_3.png)

Настройка коммутаторов производится аналогично.
### 2. Создание сетей VLAN и назначение портов коммутатора
На коммутаторе S1 создадим VLAN, согласно топологии, присвоим им имена:

![](https://github.com/alexander-ru/otus/blob/main/lab_1/create_vlan.png)

Настроим интерфейс управления и шлюз по умолчанию на коммутаторе, назначим все неиспользуемые порты (один) в VLAN ParkingLot, настроим его для
статического режима доступа и деактивируем его административно. А также назначим используемый порт E0/2 коммутатора S1 VLAN 3 и переведем его в режим access:

![](https://github.com/alexander-ru/otus/blob/main/lab_1/switch_settings_1.png)
![](https://github.com/alexander-ru/otus/blob/main/lab_1/switch_settings_2.png)

На коммутаторе S2 нужно проделать аналогичные действия.
### 3. Настройка канала 802.1Q между коммутаторами
Порты на коммутаторах, которые соединены с другим коммутатором/роутером сделаем транковыми, укажем тип инкапсуляции, разрешенные на транке VLAN-ы и укажем VLAN 8 как Native VLAN:

![](https://github.com/alexander-ru/otus/blob/main/lab_1/switch_1_trunk.png)

Тоже самое нужно сделать на втором коммутаторе.
### 4. Настройка маршрутизации между VLAN на маршрутизаторе
На маршрутизаторе создадим субинтерфейсы, соответствующие номерам VLAN (E0/0.3 = VLAN 3, E0/0.4 = VLAN 4), укажем инкапсуляцию, назначим им соответствующие IP-адреса и включим их:

![](https://github.com/alexander-ru/otus/blob/main/lab_1/router_subif.png)

Проверяем созданные субинтерфейсы:

![](https://github.com/alexander-ru/otus/blob/main/lab_1/router_interfaces.png)

### 5. Проверка работы маршрутизации между VLAN
Чуть не забыл назначить сетевые параметры на PC-A и PC-B:

![](https://github.com/alexander-ru/otus/blob/main/lab_1/pc-a_ip_config.png)
![](https://github.com/alexander-ru/otus/blob/main/lab_1/pc-b_ip_config.png)

Отправляем эхо-запросы с PC-A:

![](https://github.com/alexander-ru/otus/blob/main/lab_1/pc-a_ping.png)

С PC-B:

![](https://github.com/alexander-ru/otus/blob/main/lab_1/pc-b_ping.png)
