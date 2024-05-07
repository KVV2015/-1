# Лабораторная работа. Развертывание коммутируемой сети с резервными каналами

### Топология сети

[![2024-05-07-12-04-09.png](https://i.postimg.cc/8C96V653/2024-05-07-12-04-09.png)](https://postimg.cc/gwyJVr3q)


### Таблица адресации

|    <br>Устройство    	|    <br>Интерфейс    	|    <br>IP-адрес       	|    <br>Маска подсети    	|
|----------------------	|---------------------	|-----------------------	|-------------------------	|
|    <br>S1            	|    <br>VLAN 1       	|    <br>192.168.1.1    	|    <br>255.255.255.0    	|
|    <br>S2            	|    <br>VLAN 1       	|    <br>192.168.1.2    	|    <br>255.255.255.0    	|
|    <br>S3            	|    <br>VLAN 1       	|    <br>192.168.1.3    	|    <br>255.255.255.0    	|

## Цель работы

__Часть 1. Создание сети и настройка основных параметров устройства__

__Часть 2. Выбор корневого моста__

__Часть 3. Наблюдение за процессом выбора протоколом STP порта, исходя из стоимости 
портов__

__Часть 4. Наблюдение за процессом выбора протоколом STP порта, исходя из приоритета портов__

##     Общие сведения/сценарий

Избыточность позволяет увеличить доступность устройств в топологии сети за счёт устранения единой точки отказа. Избыточность в коммутируемой сети обеспечивается посредством использования нескольких коммутаторов или нескольких каналов между коммутаторами. Когда в проекте сети используется физическая избыточность, возможно возникновение петель и дублирование кадров.

Протокол __spanning-tree (STP)__ был разработан как механизм предотвращения возникновения петель на 2-м уровне для избыточных каналов коммутируемой сети. Протокол STP обеспечивает наличие только одного логического пути между всеми узлами назначения в сети путем намеренного блокирования резервных путей, которые могли бы вызвать петлю.

В этой лабораторной работе команда __show spanning-tree__ используется для наблюдения за процессом выбора протоколом STP корневого моста. Также вы будете наблюдать за процессом выбора портов с учетом стоимости и приоритета.

## Часть 1:	Создание сети и настройка основных параметров устройства

Настройка сети и основных параметров коммутаторов

### Шаг 1:	Создайте сеть согласно топологии.

Подключите устройства, как показано в топологии, и подсоедините необходимые кабели.

### Шаг 2:	Выполните инициализацию и перезагрузку коммутаторов.

### Шаг 3:	Настройте базовые параметры каждого коммутатора.

a.	Отключите поиск DNS.

b.	Присвойте имена устройствам в соответствии с топологией.

c.	Назначьте class в качестве зашифрованного пароля доступа к привилегированному режиму.

d.	Назначьте cisco в качестве паролей консоли и VTY и активируйте вход для консоли и VTY каналов.

e.	Настройте __logging synchronous__ для консольного канала.

f.	Настройте баннерное сообщение дня (MOTD) для предупреждения пользователей о запрете несанкционированного доступа.

g.	Задайте IP-адрес, указанный в таблице адресации для VLAN 1 на всех коммутаторах.

h.	Скопируйте текущую конфигурацию в файл загрузочной конфигурации.

```
Switch>
Switch>en
Switch#
Switch#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#host S1
S1(config)#
S1(config)#line cons
S1(config)#line console 0
S1(config-line)#
S1(config-line)#password cisco
S1(config-line)#exec-timeout 5 0
S1(config-line)#
S1(config-line)#login
S1(config-line)#
S1(config-line)#logging synchronous
S1(config-line)#
S1(config-line)#^Z
S1#
%SYS-5-CONFIG_I: Configured from console by console

S1#
S1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
S1(config)#no ip domain-lookup
S1(config)#
S1(config)#enable pass
S1(config)#enable password class
S1(config)#
S1(config)#line vty 0 4
S1(config-line)#
S1(config-line)#pass
S1(config-line)#password class
S1(config-line)#
S1(config-line)#login
S1(config-line)#
S1(config-line)#^Z
S1#
%SYS-5-CONFIG_I: Configured from console by console

S1#
S1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
S1(config)#service password-encryption 
S1(config)#
S1(config)#banner motd @++++NO PUT ENTER++++@
S1(config)#
S1(config)#^Z
S1#
%SYS-5-CONFIG_I: Configured from console by console

S1#clock set 13:04:44 06 may 2024
S1#
S1#wr
Building configuration...
[OK]
S1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
S1(config)#in
S1(config)#interface vlan1
S1(config-if)#
S1(config-if)#ip add
S1(config-if)#ip address 192.168.1.1 255.255.255.0
S1(config-if)#
S1(config-if)#
S1(config-if)#no sh
S1(config-if)#no shutdown 

S1(config-if)#
%LINK-5-CHANGED: Interface Vlan1, changed state to up

S1(config-if)#
S1(config-if)#do wr
Building configuration...
[OK]
S1(config-if)#^Z
S1#
%SYS-5-CONFIG_I: Configured from console by console

S1#
```

### Шаг 4:	Проверьте связь.

Проверим способность коммутаторов обмениваться эхо-запросами.

Успешно ли выполняется эхо-запрос от коммутатора S1 на коммутатор S2?

```
S1#ping 192.168.1.2

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.1.2, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 0/0/0 ms

S1#
```
Успешно!

Успешно ли выполняется эхо-запрос от коммутатора S1 на коммутатор S3?

```
S1#ping 192.168.1.3

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.1.3, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 0/5/28 ms

S1#
S1#ping 192.168.1.3

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.1.3, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 0/0/1 ms

S1#
```
Успешно!


Успешно ли выполняется эхо-запрос от коммутатора S2 на коммутатор S3?

```
S2#
S2#ping 192.168.1.3

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.1.3, timeout is 2 seconds:
..!!!
Success rate is 60 percent (3/5), round-trip min/avg/max = 0/0/0 ms

S2#ping 192.168.1.3

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.1.3, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 0/0/0 ms

S2#
```
Успешно!

## Часть 2.	Определение корневого моста

Для каждого экземпляра протокола __spanning-tree__ (коммутируемая сеть LAN или широковещательный домен) существует коммутатор, выделенный в качестве корневого моста. Корневой мост служит точкой привязки для всех расчётов протокола __spanning-tree__, позволяя определить избыточные пути, которые следует заблокировать.

Процесс выбора определяет, какой из коммутаторов станет корневым мостом. Коммутатор с наименьшим значением идентификатора моста (BID) становится корневым мостом. Идентификатор BID состоит из значения приоритета моста, расширенного идентификатора системы и MAC-адреса коммутатора. Значение приоритета может находиться в диапазоне от 0 до 65535 с шагом 4096. По умолчанию используется значение 32768.

### Шаг 1:	Отключите все порты на коммутаторах.

```
S1(config)#
S1(config)#interface range fa0/1-24
S1(config-if-range)#
S1(config-if-range)#shut
S1(config-if-range)#shutdown 


%LINK-5-CHANGED: Interface FastEthernet0/5, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/6, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/7, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/8, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/9, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/10, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/11, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/12, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/13, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/14, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/15, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/16, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/17, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/18, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/19, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/20, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/21, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/22, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/23, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/24, changed state to administratively down
S1(config-if-range)#
%LINK-5-CHANGED: Interface FastEthernet0/1, changed state to administratively down

%LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/1, changed state to down

%LINK-5-CHANGED: Interface FastEthernet0/2, changed state to administratively down

%LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/2, changed state to down

%LINK-5-CHANGED: Interface FastEthernet0/3, changed state to administratively down

%LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/3, changed state to down

%LINK-5-CHANGED: Interface FastEthernet0/4, changed state to administratively down

%LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/4, changed state to down

%LINEPROTO-5-UPDOWN: Line protocol on Interface Vlan1, changed state to down

S1(config-if-range)#

S1(config)#
S1(config)#interface GigabitEthernet0/1
S1(config-if)#
S1(config-if)#sh

%LINK-5-CHANGED: Interface GigabitEthernet0/1, changed state to administratively down
S1(config-if)#
S1(config-if)#ex
S1(config)#interface GigabitEthernet0/2
S1(config-if)#
S1(config-if)#sh

%LINK-5-CHANGED: Interface GigabitEthernet0/2, changed state to administratively down
S1(config-if)#
S1(config-if)#ex
S1(config)#
```

### Шаг 2:	Настройте подключенные порты в качестве транковых.

```
1(config)#
S1(config)#int rang
S1(config)#int range f0/1-4
S1(config-if-range)#
S1(config-if-range)#switchport mode trunk
S1(config-if-range)#
S1(config-if-range)#switchport trunk all
S1(config-if-range)#switchport trunk allowed vlan 1
S1(config-if-range)#
S1(config-if-range)#ex
```
Проверим настройки:

```
S1#sh run
Building configuration...

Current configuration : 1754 bytes
!
version 15.0
no service timestamps log datetime msec
no service timestamps debug datetime msec
service password-encryption
!
hostname S1
!
enable password 7 0822404F1A0A
!
!
!
no ip domain-lookup
!
!
!
spanning-tree mode pvst
spanning-tree extend system-id
!
interface FastEthernet0/1
 switchport trunk allowed vlan 1
 switchport mode trunk
 shutdown
!
interface FastEthernet0/2
 switchport trunk allowed vlan 1
 switchport mode trunk
 shutdown
!
interface FastEthernet0/3
 switchport trunk allowed vlan 1
 switchport mode trunk
 shutdown
!
interface FastEthernet0/4
 switchport trunk allowed vlan 1
 switchport mode trunk
 shutdown
!
interface FastEthernet0/5
 shutdown
!
```

### Шаг 3:	Включим порты F0/2 и F0/4 на всех коммутаторах.

```
S1(config)#int range f0/2 
S1(config-if-range)#
S1(config-if-range)#no sh

%LINK-5-CHANGED: Interface FastEthernet0/2, changed state to down
S1(config-if-range)#ex
S1(config)#int range f0/4
S1(config-if-range)#
S1(config-if-range)#no sh

%LINK-5-CHANGED: Interface FastEthernet0/4, changed state to down
S1(config-if-range)#ex
S1(config)#
```
Проверим включились ли порты

```
S1#sh run
Building configuration...

Current configuration : 1734 bytes
!
version 15.0
no service timestamps log datetime msec
no service timestamps debug datetime msec
service password-encryption
!
hostname S1
!
enable password 7 0822404F1A0A
!
!
!
no ip domain-lookup
!
!
!
spanning-tree mode pvst
spanning-tree extend system-id
!
interface FastEthernet0/1
 switchport trunk allowed vlan 1
 switchport mode trunk
 shutdown
!
interface FastEthernet0/2
 switchport trunk allowed vlan 1
 switchport mode trunk
!
interface FastEthernet0/3
 switchport trunk allowed vlan 1
 switchport mode trunk
 shutdown
!
interface FastEthernet0/4
 switchport trunk allowed vlan 1
 switchport mode trunk
!
interface FastEthernet0/5
 shutdown
!
```
Порты поднялись.


### Шаг 4:	Отобразите данные протокола __spanning-tree__.

Введите команду __show spanning-tree__ на всех трех коммутаторах. Приоритет идентификатора моста рассчитывается путем сложения значений приоритета и расширенного идентификатора системы. 
Расширенным идентификатором системы всегда является номер сети VLAN. В примере ниже все три коммутатора имеют равные значения приоритета идентификатора моста (32769 = 32768 + 1, где приоритет по умолчанию = 32768, номер сети VLAN = 1); следовательно, коммутатор с самым низким значением MAC-адреса становится корневым мостом.

На коммутаторе S1

```
S1#
S1#show spanning-tree
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     0006.2A6C.B1AB
             Cost        19
             Port        2(FastEthernet0/2)
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     00D0.5814.5125
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  20

Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/2            Root FWD 19        128.2    P2p
Fa0/4            Altn BLK 19        128.4    P2p

S1#

```
На коммутаторе S2
```
S2#
S2#show spanning-tree
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     0006.2A6C.B1AB
             This bridge is the root
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     0006.2A6C.B1AB
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  20

Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/2            Desg FWD 19        128.2    P2p
Fa0/4            Desg FWD 19        128.4    P2p

S2#


```
На коммутаторе S3
```
S3#
S3#sh spa
S3#sh spanning-tree 
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     0006.2A6C.B1AB
             Cost        19
             Port        2(FastEthernet0/2)
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     0060.5C1E.5A22
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  20

Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/2            Root FWD 19        128.2    P2p
Fa0/4            Desg FWD 19        128.4    P2p

S3#
```

В схему ниже запишите роль и состояние (STS) активных портов на каждом коммутаторе в топологии:

[![7.jpg](https://i.postimg.cc/RFtw48qn/7.jpg)](https://postimg.cc/YGpvxd6p)


С учетом выходных данных, поступающих с коммутаторов, мы видим следующие параменты:

- Коммутатор являющийся корневым мостом (root) - S2

- Данный коммутатор был выбран протоколом STP в качерстве Рута из за наименьшего MAC адреса.

- Корневыми портами являются:

   S1 - порт 2

   S3 - порт 2

- Назначенными портами являются:

  S2 - порты 2 и 4

  S3 - порт 4

- Альтернативный порт:

  S1 - порт 4 - в настоящее время заблокирован

- Протокол __spanning-tree__ выбрал этот порт в качестве невыделенного (заблокированного) порта, из за высокой стоимости доставки пакетов через этот порт.

## Часть 3:	Наблюдение за процессом выбора протоколом STP порта, исходя из стоимости портов

Алгоритм протокола __spanning-tree (STA)__ использует корневой мост, как точку привязки, после чего определяет, какие порты будут заблокированы, исходя из стоимости пути. Порт с более низкой стоимостью пути является предпочтительным. Если стоимости портов равны, процесс сравнивает BID. Если BID равны, для определения корневого моста используются приоритеты портов. Наиболее низкие значения являются предпочтительными. В части 3 вам предстоит изменить стоимость порта, чтобы определить, какой порт будет заблокирован протоколом __spanning-tree__.

### Шаг 1:	Определите коммутатор с заблокированным портом.

При текущей конфигурации только один коммутатор может содержать заблокированный протоколом STP порт. Выполните команду __show spanning-tree__ на обоих коммутаторах некорневого моста.

Для коммутатора S1:
```
S1#
S1#sh sp
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
  !
  !
  Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/2            Root FWD 19        128.2    P2p
Fa0/4            Altn BLK 19        128.4    P2p

S1#
```

Для коммутатора S3:

```
S3#
S3#sh sp
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
  !
  !
  !
  Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/2            Root FWD 19        128.2    P2p
Fa0/4            Desg FWD 19        128.4    P2p

S3#
```
 Мы видим, что на коммутаторе S1 заблокирован Порт F0/4.

### Шаг 2:	Измените стоимость порта.

Помимо заблокированного порта, единственным активным портом на этом коммутаторе является порт, выделенный в качестве порта корневого моста. 

Уменьшите стоимость этого порта, корневого моста до 18, выполнив команду __spanning-tree vlan 1 cost 18__ режима конфигурации интерфейса.

```
S1(config)#
S1(config)#int fa 0/2
S1(config-if)#spanning-tree vlan 1 cost 18
S1(config-if)#
S1(config-if)#ex
S1(config)#
S1(config)#do wr
Building configuration...
[OK]
S1(config)#

```

### Шаг 3:	Просмотрите изменения протокола spanning-tree.

Повторно выполните команду __show spanning-tree__ на обоих коммутаторах некорневого моста. 

Обратите внимание, что ранее заблокированный порт (S1 – F0/4) теперь является назначенным портом, и протокол spanning-tree теперь блокирует порт на другом коммутаторе некорневого моста (S3 – F0/4).

```
S1#sh sp
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     0006.2A6C.B1AB
             Cost        18
             Port        2(FastEthernet0/2)
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     00D0.5814.5125
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  20

Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/2            Root FWD 18        128.2    P2p
Fa0/4            Desg LSN 19        128.4    P2p

S1#
```

Зто произошло из за то, что уменьшилась суммарная стоимость доставки пакетов до рута, через этот порт.

### Шаг 4:	Удалите изменения стоимости порта.

a.	Выполните команду __no spanning-tree vlan 1 cost 18__ режима конфигурации интерфейса, чтобы удалить запись стоимости, созданную ранее.

```
S1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
S1(config)#int fa 0/2
S1(config-if)#
S1(config-if)#no spanning-tree vlan 1 cost 18
S1(config-if)#
S1(config-if)#^Z
S1#
%SYS-5-CONFIG_I: Configured from console by console

S1#
```

b.	Повторно выполните команду __show spanning-tree__, чтобы подтвердить, что протокол STP сбросил порт на коммутаторе некорневого моста, вернув исходные настройки порта. Протоколу STP требуется примерно 30 секунд, чтобы завершить процесс перевода порта.

```
S1#sh sp
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     0006.2A6C.B1AB
             Cost        19
             Port        2(FastEthernet0/2)
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     00D0.5814.5125
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  20

Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/2            Root FWD 19        128.2    P2p
Fa0/4            Altn BLK 19        128.4    P2p

S1#
```
Для более быстрой отработки протоколом событий, изменим версию протокола spanning-tree, с классического __PVST__, на более новый __rapid-pvst__, введя команду __spanning-tree mode rapid-pvst__, в режиме глобальной конфигурации.

```
S1(config)#
S1(config)#spa
S1(config)#spanning-tree mode rapi
S1(config)#spanning-tree mode rapid-pvst 
S1(config)#
```

## Часть 4.	Наблюдение за процессом выбора протоколом STP порта, исходя из приоритета портов.

Если стоимости портов равны, процесс сравнивает BID. Если BID равны, для определения корневого моста используются приоритеты портов. Значение приоритета по умолчанию — 128. STP объединяет приоритет порта с номером порта, чтобы разорвать связи. Наиболее низкие значения являются предпочтительными. В части 4 вам предстоит активировать избыточные пути до каждого из коммутаторов, чтобы просмотреть, каким образом протокол STP выбирает порт с учетом приоритета портов.

a.	Включите порты __F0/1 и F0/3__ на всех коммутаторах.

b.	Подождите 30 секунд, чтобы протокол STP завершил процесс перевода порта, после чего выполните команду __show spanning-tree__ на коммутаторах некорневого моста.

__Обратите внимание__, порт корневого моста переместился на порт с меньшим номером, связанный с коммутатором корневого моста, и заблокировал предыдущий порт корневого моста.

На коммутаторе S1
```
S1#sh sp
VLAN0001
  Spanning tree enabled protocol rstp
  Root ID    Priority    32769
             Address     0006.2A6C.B1AB
             Cost        19
             Port        1(FastEthernet0/1)
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     00D0.5814.5125
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  20

Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/1            Root FWD 19        128.1    P2p
Fa0/2            Altn BLK 19        128.2    P2p
Fa0/3            Altn BLK 19        128.3    P2p
Fa0/4            Altn BLK 19        128.4    P2p

S1#

```
На коммутаторе S3
```

S3#sh sp
VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     0006.2A6C.B1AB
             Cost        19
             Port        1(FastEthernet0/1)
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     0060.5C1E.5A22
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  20

Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Fa0/1            Root FWD 19        128.1    P2p
Fa0/2            Altn BLK 19        128.2    P2p
Fa0/3            Desg FWD 19        128.3    P2p
Fa0/4            Desg FWD 19        128.4    P2p

S3#
```


Портом корневого моста теперь являются:
- На S1 - порт № 1
- на S3 - порт № 1


Протокол выбрал эти порты так как значения BID на этих коммутаторах одинаковые, а следовательно приоритет ведется по наименьшей нумерации портов, связанных с коммутатором корневого моста.


###     Вопросы для повторения
1.	Какое значение протокол STP использует первым после выбора корневого моста, чтобы определить выбор порта?

стоимости портов
_______________________________________________________________________________________
2.	Если первое значение на двух портах одинаково, какое следующее значение будет использовать протокол STP при выборе порта?

Brege ID
_______________________________________________________________________________________
3.	Если оба значения на двух портах равны, каким будет следующее значение, которое использует протокол STP при выборе порта?

Последним условием является порядковый номер порта, чем ниже тем актуальнее.
``
