я# Лабораторная работа - Внедрение маршрутизации между виртуальными локальными сетями

### Топологическая схема сети:

[![2024-04-25-13-59-10.png](https://i.postimg.cc/TPGZYLKk/2024-04-25-13-59-10.png)](https://postimg.cc/sM6nT29S)

### Таблица адресации

|     <br>Устройство     	|     <br>Интерфейс     	|     <br>IP-адрес        	|     <br>Маска    подсети     	|     <br>Шлюз    по умолчанию     	|
|------------------------	|-----------------------	|-------------------------	|------------------------------	|----------------------------------	|
|    <br>R1              	|    <br>G0/0/1.10      	|    <br>192.168.10.1     	|    <br>255.255.255.0         	|    <br>—                         	|
|    <br>R1              	|    <br>G0/0/1.20      	|    <br>192.168.20.1     	|    <br>255.255.255.0         	|    <br>—                         	|
|    <br>R1              	|    <br>G0/0/1.30      	|    <br>192.168.30.1     	|    <br>255.255.255.0         	|    <br>—                         	|
|    <br>R1              	|    <br>G0/0/1.1000    	|    <br>—                	|    <br>—                     	|    <br>—                         	|
|    <br>S1              	|    <br>VLAN 10        	|    <br>192.168.10.11    	|    <br>255.255.255.0         	|    <br>192.168.10.1              	|
|    <br>S2              	|    <br>VLAN 10        	|    <br>192.168.10.12    	|    <br>255.255.255.0         	|    <br>192.168.10.1              	|
|    <br>PC-A            	|    <br>NIC            	|    <br>192.168.20.3     	|    <br>255.255.255.0         	|    <br>192.168.20.1              	|
|    <br>PC-B            	|    <br>NIC            	|    <br>192.168.30.3     	|    <br>255.255.255.0         	|    <br>192.168.30.1              	|


###  Таблица VLAN

|    <br>VLAN    	|    <br>Имя            	|    <br>Назначенный интерфейс                                                  	|
|----------------	|-----------------------	|-------------------------------------------------------------------------------	|
|    <br>10      	|    <br>Управление     	|    <br>S1: VLAN 10 <br>   <br>S2: VLAN 10                                     	|
|    <br>20      	|    <br>Sales          	|    <br>S1: F0/6                                                               	|
|    <br>30      	|    <br>Operations     	|    <br>S2: F0/18                                                              	|
|    <br>999     	|    <br>Parking_Lot    	|    <br>С1: F0/2-4, F0/7-24, G0/1-2<br>   <br>С2: F0/2-17, F0/19-24, G0/1-2    	|
|    <br>1000    	|    <br>Собственная    	|    <br>—                                                                      	|

## Задачи

 Часть 1. Создание сети и настройка основных параметров устройства

Часть 2. Создание сетей VLAN и назначение портов коммутатора

Часть 3. Настройка транка 802.1Q между коммутаторами.

Часть 4. Настройка маршрутизации между сетями VLAN

Часть 5. Проверка, что маршрутизация между VLAN работает


## Общие сведения 

В целях повышения производительности сети большие широковещательные домены 2-го уровня делят на домены меньшего размера. Для этого современные коммутаторы используют виртуальные локальные сети (VLAN). VLAN также можно использовать в качестве меры безопасности, отделяя конфиденциальный трафик данных от остальной части сети. Сети VLAN облегчают процесс проектирования сети, обеспечивающей помощь в достижении целей организации. Для связи между VLAN требуется устройство, работающее на уровне 3 модели OSI. Добавление маршрутизации между VLAN позволяет организации разделять и разделять широковещательные домены, одновременно позволяя им обмениваться данными друг с другом.

Транковые каналы сети VLAN используются для распространения сетей VLAN по различным устройствам. Транковые каналы разрешают передачу трафика из множества сетей VLAN через один канал, не нанося вред идентификации и сегментации сети VLAN. Особый вид маршрутизации между VLAN, называемый «Router-on-a-Stick», использует магистраль от маршрутизатора к коммутатору, чтобы все VLAN могли переходить к маршрутизатору.

## Сценарий

В этой лабораторной работе вы создадите VLAN на обоих коммутаторах в топологии, назначите VLAN для коммутации портов доступа, убедитесь, что VLAN работают должным образом, создадите транки VLAN между двумя коммутаторами и между S1 и R1, и настройте маршрутизацию между VLAN на R1 для разрешения связи между хостами в разных VLAN независимо от подсети, в которой находится хост.

### Требуемые ресурсы

•	1 Маршрутизатор (Cisco 4221 с универсальным образом Cisco IOS XE версии 16.9.4 или аналогичным)

•	2 коммутатора (Cisco 2960 с операционной системой Cisco IOS 15.2(2) (образ lanbasek9) или аналогичная модель)

•	2 ПК (ОС Windows с программой эмуляции терминалов, такой как Tera Term)

•	Консольные кабели для настройки устройств Cisco IOS через консольные порты.

•	Кабели Ethernet, расположенные в соответствии с топологией

## Инструкции

### Часть 1. Создание сети и настройка основных параметров устройства
В первой части лабораторной работы вам предстоит создать топологию сети и настроить базовые параметры для узлов ПК и коммутаторов.

#### Шаг 1. Создайте сеть согласно топологии.
Подключите устройства, как показано в топологии, и подсоедините необходимые кабели.

#### Шаг 2. Настройте базовые параметры для маршрутизатора.

a.	Подключитесь к маршрутизатору с помощью консоли и активируйте привилегированный режим EXEC. Откройте окно конфигурации

b.	Войдите в режим конфигурации.

c.	Назначьте маршрутизатору имя устройства.

d.	Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов. no ip domain-lookup

e.	Назначьте __class__ в качестве зашифрованного пароля привилегированного режима EXEC.

f.	Назначьте __cisco__ в качестве пароля консоли и включите вход в систему по паролю.

g.	Установите __cisco__ в качестве пароля виртуального терминала и активируйте вход.

h.	Зашифруйте открытые пароли.

i.	Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.

j.	Сохраните текущую конфигурацию в файл загрузочной конфигурации.

k.	Настройте на маршрутизаторе время.

```
outer>en
Router#
Router#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Router(config)#
Router(config)#host R1
R1(config)#
1(config)#no ip domain-lo
R1(config)#no ip domain-lookup 
R1(config)#
R1(config)#enabl
R1(config)#enable pass
R1(config)#enable password class
R1(config)#
R1(config)#line cons
R1(config)#line console 0
R1(config-line)#
R1(config-line)#pass
R1(config-line)#password cisco
R1(config-line)#
R1(config-line)#login
R1(config-line)#
R1(config-line)#^Z
R1#
%SYS-5-CONFIG_I: Configured from console by console

R1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#line vty 0 4
R1(config-line)#
R1(config-line)#pass
R1(config-line)#password cisco
R1(config-line)#
R1(config-line)#login
R1(config-line)#
R1(config-line)#^Z
R1#
%SYS-5-CONFIG_I: Configured from console by console

R1#
R1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#serv
R1(config)#service pass
R1(config)#service password-encryption 
R1(config)#
R1(config)#banner mo
R1(config)#banner motd @++++NO PUT ENTER++++@
R1(config)#
R1(config)#do wr
Building configuration...
[OK]
R1(config)#
R1(config)#^Z
R1#
%SYS-5-CONFIG_I: Configured from console by console

R1#clock set 15:04:44 25 april 2024
R1#
R1#
```



#### Шаг 3 Настройте базовые параметры каждого коммутатора.

a.	Присвойте коммутатору имя устройства.

b.	Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.

c.	Назначьте __class__ в качестве зашифрованного пароля привилегированного режима EXEC.

d.	Назначьте __cisco__ в качестве пароля консоли и включите вход в систему по паролю.

e.	Установите __cisco__ в качестве пароля виртуального терминала и активируйте вход.

f.	Зашифруйте открытые пароли.

g.	Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.

h.	Настройте на коммутаторах время.

i.	Сохранение текущей конфигурации в качестве начальной.

```
Switch>
Switch>en
Switch#
Switch#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#
Switch(config)#host S1
S1(config)#
S1(config)#no dns dom
S1(config)#no dom
S1(config)#no doma
S1(config)#no ip domain-lookup
S1(config)#
S1(config)#
S1(config)#ena
S1(config)#enable sec
S1(config)#enable secret class
S1(config)#
S1(config)#line cons
S1(config)#line console 0
S1(config-line)#
S1(config-line)#pass
S1(config-line)#password cisco
S1(config-line)#
S1(config-line)#login
S1(config-line)#
S1(config-line)#^Z
S1#
%SYS-5-CONFIG_I: Configured from console by console

S1#
S1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
S1(config)#lin
S1(config)#line vty 0 4
S1(config-line)#
S1(config-line)#pass
S1(config-line)#password cisco
S1(config-line)#
S1(config-line)#login
S1(config-line)#
S1(config-line)#^Z
S1#
%SYS-5-CONFIG_I: Configured from console by console

S1#
S1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
S1(config)#serv
S1(config)#service pass
S1(config)#service password-encryption 
S1(config)#
S1(config)#bann
S1(config)#banner mo
S1(config)#banner motd @++++++NO PUT ENTER++++@
S1(config)#
S1(config)#^Z
S1#
%SYS-5-CONFIG_I: Configured from console by console

S1#
S1#
S1#cloc
S1#clock set 15:07:44 25 april 2024
S1#
S1#
S1#wr
Building configuration...
[OK]
S1#
```

#### Шаг 4. Настройте узлы ПК.

Настроим адрес для PC-A


```
C:\>ipconfig

FastEthernet0 Connection:(default port)

   Connection-specific DNS Suffix..: 
   Link-local IPv6 Address.........: FE80::2E0:A3FF:FE91:439D
   IPv6 Address....................: ::
   IPv4 Address....................: 192.168.20.3
   Subnet Mask.....................: 255.255.255.0
   Default Gateway.................: ::
                                     192.168.20.1

```
Настроим адрес для PC-B

```
C:\>
C:\>ipconfig

FastEthernet0 Connection:(default port)

   Connection-specific DNS Suffix..: 
   Link-local IPv6 Address.........: FE80::202:16FF:FE2A:465
   IPv6 Address....................: ::
   IPv4 Address....................: 192.168.30.3
   Subnet Mask.....................: 255.255.255.0
   Default Gateway.................: ::
                                     192.168.30.1

```

### Часть 2. Создание сетей VLAN и назначение портов коммутатора

Во второй части мы создадим VLAN, как указано в таблице выше, на обоих коммутаторах. Затем назначим VLAN соответствующему интерфейсу и проверим настройки конфигурации. Выполним следующие задачи на каждом коммутаторе:

#### Шаг 1. Создайте сети VLAN на коммутаторах.

a.	Создадим и назовем необходимые VLAN на каждом коммутаторе из таблицы выше. Откроем окно конфигурации

```
S1(config)#
S1(config)#vlan 10
S1(config-vlan)#
S1(config-vlan)#ip add
S1(config-vlan)#name Uprava
S1(config-vlan)#
S1(config-vlan)#vlan 20 
S1(config-vlan)#
1(config-vlan)#name Sales
S1(config-vlan)#
S1(config-vlan)#vlan 30
S1(config-vlan)#
S1(config-vlan)#na
S1(config-vlan)#name Operations
S1(config-vlan)#
S1(config-vlan)#vlan 999
S1(config-vlan)#
S1(config-vlan)#name Parking_Lot
S1(config-vlan)#
```

b.	Настроим интерфейс управления и шлюз по умолчанию на каждом коммутаторе, используя информацию об IP-адресе в таблице адресации.

```
S1(config)#interface vlan 10
S1(config-if)#
%LINK-5-CHANGED: Interface Vlan10, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface Vlan10, changed state to up

S1(config-if)#
S1(config-if)#ip add
S1(config-if)#ip address 192.168.10.11 255.255.255.0
S1(config-if)#
S1(config-if)#description default gateway for 192.168.10.1
S1(config-if)#
S1(config-if)#no sh
```

c.	Назначим все неиспользуемые порты коммутатора VLAN именем __Parking_Lot__, настроим их для статического режима доступа и административно деактивируем их.

__Примечание__. Команда __interface range__ полезна для выполнения этой задачи с минимальным количеством команд.

```
S1(config)#
S1(config)#vlan 999
S1(config-vlan)#
S1(config-vlan)#
S1(config-vlan)#name Parking_Lot
S1(config-vlan)#ex
S1(config)#
S1(config)#do wr
S1(config)#interface rang
S1(config)#interface range F0/2-4, F0/7-24, G0/1-2
S1(config-if-range)#
S1(config-if-range)#swi
S1(config-if-range)#switchport mod
S1(config-if-range)#switchport mode acc
S1(config-if-range)#switchport mode access 
S1(config-if-range)#
S1(config-if-range)#sw
S1(config-if-range)#switchport acc
S1(config-if-range)#switchport access vlan 999
S1(config-if-range)#
S1(config-if-range)#
```

#### Шаг 2. Назначим сети VLAN соответствующим интерфейсам коммутатора.

a.	Назначим используемые порты соответствующей VLAN (указанной в таблице VLAN выше) и настроим их для режима статического доступа.

```
S1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
S1(config)#
S1(config)#
S1(config)#int fa0/6
S1(config-if)#
S1(config-if)#switchport mode access 
S1(config-if)#
S1(config-if)#switchport access vlan 20
S1(config-if)#
S1(config-if)#ex
```

```
S2#
S2#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
S2(config)#interface fa0/18
S2(config-if)#
S2(config-if)#switc
S2(config-if)#switchport mode
S2(config-if)#switchport mode acc
S2(config-if)#switchport mode access 
S2(config-if)#
S2(config-if)#swi
S2(config-if)#switchport acc
S2(config-if)#switchport access vlan 30
S2(config-if)#
S2(config-if)#^Z
```

b.	Убедимся, что VLAN назначены на правильные интерфейсы.

```
S2#sh vl

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    
10   uprava                           active    
20   operation                        active    
30   Sales                            active    Fa0/18
999  Parking_Lot                      active    Fa0/2, Fa0/3, Fa0/4, Fa0/5
                                                Fa0/6, Fa0/7, Fa0/8, Fa0/9
                                                Fa0/10, Fa0/11, Fa0/12, Fa0/13
                                                Fa0/14, Fa0/15, Fa0/16, Fa0/17
                                                Fa0/19, Fa0/20, Fa0/21, Fa0/22
                                                Fa0/23, Fa0/24, Gig0/1, Gig0/2
1000 Native                           active    
1002 fddi-default                     active    
1003 token-ring-default               active    
1004 fddinet-default                  active    
1005 trnet-default                    active 
```



### Часть 3. Конфигурация магистрального канала стандарта 802.1Q между коммутаторами

В части 3 мы вручную настроим интерфейс Fа0/1 как транк.

#### Шаг 1. Вручную настройте магистральный интерфейс Fa0/1 на коммутаторах S1 и S2.

a.	Настройка статического транкинга на интерфейсе F0/1 для обоих коммутаторов.
Откройте окно конфигурации

b.	Установите native VLAN 1000 на обоих коммутаторах.

```
2(config-if)#
S2(config-if)#swi
S2(config-if)#switchport tr
S2(config-if)#switchport trunk nat
S2(config-if)#switchport trunk native vlan 1000
S2(config-if)#
```

c.	Укажите, что VLAN 10, 20, 30 и 1000 могут проходить по транку.

```
2#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
S2(config)#int f0/1
S2(config-if)#
S2(config-if)#switchport trunk allowed vlan 10,20,30,1000
S2(config-if)#
```

d.	Проверьте транки, native VLAN и разрешенные VLAN через транк.

```
S1#sh int tr
Port        Mode         Encapsulation  Status        Native vlan
Fa0/1       on           802.1q         trunking      1000

Port        Vlans allowed on trunk
Fa0/1       10,20,30,1000

Port        Vlans allowed and active in management domain
Fa0/1       10,30,1000

Port        Vlans in spanning tree forwarding state and not pruned
Fa0/1       10,30,1000

S2#
```
Настроим для коммутатора шлюз по умолчанию 

```
S1#
S1 (config)#ip default-gateway 192.168.10.1
S1 (config)#exit
S1#
```

Проверим настройки 

```
S1#sh run
Building configuration...

Current configuration : 3005 bytes
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
 switchport trunk native vlan 1000
 switchport trunk allowed vlan 10,20,30,1000
 switchport mode trunk
!
!
interface FastEthernet0/5
 switchport trunk native vlan 1000
 switchport trunk allowed vlan 10,20,30,1000
 switchport mode trunk
!
!
interface FastEthernet0/24
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface GigabitEthernet0/1
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface GigabitEthernet0/2
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface Vlan1
 no ip address
 shutdown
!
interface Vlan10
 description default gateway for 192.168.10.1
 ip address 192.168.10.11 255.255.255.0
!
ip default-gateway 192.168.10.1
!
```

#### Шаг 2. Вручную настройте магистральный интерфейс Fa0/5 на коммутаторе S1.

a.	Настройте интерфейс S1 F0/5 с теми же параметрами транка, что и F0/1. Это транк до маршрутизатора.

b.	Сохраните текущую конфигурацию в файл загрузочной конфигурации.

```
S1(config)#int fa 0/5
S1(config-if)#
S1(config-if)#switchport mode trunk
S1(config-if)#
S1(config-if)#switchport trunk allowed vlan 10,20,30
S1(config-if)#
S1(config-if)#switchport trunk native vlan 1000
S1(config-if)#
```

c.	Проверка транкинга.

```
S1#sh run
Building configuration...

Current configuration : 3064 bytes
!
version 15.0
no service timestamps log datetime msec
no service timestamps debug datetime msec
service password-encryption
!
hostname S1
!
enable secret 5 $1$mERr$9cTjUIEqNGurQiFU.ZeCi1
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
 switchport access vlan 10
 switchport trunk native vlan 1000
 switchport trunk allowed vlan 10,20,30
 switchport mode trunk
!
interface FastEthernet0/2
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface FastEthernet0/3
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface FastEthernet0/4
 switchport access vlan 999
 switchport mode access
 shutdown
!
interface FastEthernet0/5
 switchport access vlan 10
 switchport trunk native vlan 1000
 switchport trunk allowed vlan 10,20,30
 switchport mode trunk

 ip default-gateway 192.168.10.1
```

__Вопрос:__
Что произойдет, если G0/1 на R1 будет отключен?

Если выключить порт g0/1 то работа vlan остановится
```
R1#sh ip int br
Interface              IP-Address      OK? Method Status                Protocol 
GigabitEthernet0/0     unassigned      YES unset  administratively down down 
GigabitEthernet0/1     unassigned      YES unset  administratively down down 
GigabitEthernet0/1.10  192.168.10.1    YES manual administratively down down 
GigabitEthernet0/1.20  192.168.20.1    YES manual administratively down down 
GigabitEthernet0/1.30  192.168.30.1    YES manual administratively down down 
GigabitEthernet0/1.1000unassigned      YES unset  administratively down down 
GigabitEthernet0/2     unassigned      YES unset  administratively down down 
Vlan1                  unassigned      YES unset  administratively down down
R1#
```
Попробуем отправить эхо запрос с PC-A на шлюз по умолчанию при выключенном интерфейсе

```
C:\>ping 192.168.30.1

Pinging 192.168.30.1 with 32 bytes of data:

Request timed out.
Request timed out.

Ping statistics for 192.168.30.1:
    Packets: Sent = 3, Received = 0, Lost = 3 (100% loss),

Control-C
^C
C:\>
```


### Часть 4. Настройка маршрутизации между сетями VLAN

#### Шаг 1. Настройте маршрутизатор.

Откройте окно конфигурации

a.	При необходимости активируйте интерфейс G0/1 на маршрутизаторе.

b.	Настройте подинтерфейсы для каждой VLAN, как указано в таблице IP-адресации. Все подинтерфейсы используют инкапсуляцию 802.1Q. Убедитесь, что подинтерфейсу для native VLAN не назначен IP-адрес. Включите описание для каждого подинтерфейса.

```
R1(config)#
R1(config)#int g0/1.10
R1(config-subif)#
R1(config-subif)#desc
R1(config-subif)#description default gateway for vlan 10
R1(config-subif)#enc
R1(config-subif)#encapsulation d
R1(config-subif)#encapsulation dot1Q 10
R1(config-subif)#å
R1(config-subif)#ip add
R1(config-subif)#ip address 192.168.10.1 255.255.255.0
R1(config-subif)#
R1(config-subif)#no sh
R1(config-subif)#
R1(config-subif)#ex
R1(config)#
R1(config)#int g0/1.20
R1(config-subif)#
R1(config-subif)#description default gateway for vlan 20
R1(config-subif)#
R1(config-subif)#encapsulation dot1Q 20
R1(config-subif)#
R1(config-subif)#ip address 192.168.20.1 255.255.255.0
R1(config-subif)#
R1(config-subif)#no sh
R1(config-subif)#
R1(config-subif)#ex
R1(config)#
R1(config)#int g0/1.30
R1(config-subif)#
R1(config-subif)#description default gateway for vlan 30
R1(config-subif)#encapsulation dot1Q 30
R1(config-subif)#ip address 192.168.30.1 255.255.255.0
R1(config-subif)#
R1(config-subif)#no sh
R1(config-subif)#ex
R1(config)#
R1(config)#int g0/1.1000
R1(config-subif)#
R1(config-subif)#description default gateway for vlan 1000
R1(config-subif)#
R1(config-subif)#encapsulation dot1Q 1000 Native
R1(config-subif)#
R1(config-subif)#no sh
R1(config-subif)#
R1(config-subif)#ex
R1(config)#
R1(config)#int g0/1
R1(config-if)#
R1(config-if)#desc
R1(config-if)#description trunk link to S1
R1(config-if)#
R1(config-if)#no sh

R1(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet0/1, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/1, changed state to up

%LINK-5-CHANGED: Interface GigabitEthernet0/1.10, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/1.10, changed state to up

%LINK-5-CHANGED: Interface GigabitEthernet0/1.20, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/1.20, changed state to up

%LINK-5-CHANGED: Interface GigabitEthernet0/1.30, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/1.30, changed state to up

%LINK-5-CHANGED: Interface GigabitEthernet0/1.1000, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/1.1000, changed state to up

R1(config-if)#
```

c.	Убедитесь, что вспомогательные интерфейсы работают.

```
!
interface GigabitEthernet0/1
 description trunk link to S1
 no ip address
 duplex auto
 speed auto
!
interface GigabitEthernet0/1.10
 description default gateway for vlan 10
 encapsulation dot1Q 10
 ip address 192.168.10.1 255.255.255.0
!
interface GigabitEthernet0/1.20
 description default gateway for vlan 20
 encapsulation dot1Q 20
 ip address 192.168.20.1 255.255.255.0
!
interface GigabitEthernet0/1.30
 description default gateway for vlan 30
 encapsulation dot1Q 30
 ip address 192.168.30.1 255.255.255.0
!
interface GigabitEthernet0/1.1000
 description default gateway for vlan 1000
 encapsulation dot1Q 1000 native
 no ip address
!
interface GigabitEthernet0/2
 no ip address
 duplex auto
 speed auto
 shutdown
!
interface Vlan1
 no ip address
 shutdown
```
```
R1#sh ip route 
Gateway of last resort is not set

     192.168.10.0/24 is variably subnetted, 2 subnets, 2 masks
C       192.168.10.0/24 is directly connected, GigabitEthernet0/1
L       192.168.10.1/32 is directly connected, GigabitEthernet0/1
     192.168.20.0/24 is variably subnetted, 2 subnets, 2 masks
C       192.168.20.0/24 is directly connected, GigabitEthernet0/1.20
L       192.168.20.1/32 is directly connected, GigabitEthernet0/1.20
     192.168.30.0/24 is variably subnetted, 2 subnets, 2 masks
C       192.168.30.0/24 is directly connected, GigabitEthernet0/1.30
L       192.168.30.1/32 is directly connected, GigabitEthernet0/1.30

R1#
```
```
R1#sh ip int br
Interface              IP-Address      OK? Method Status                Protocol 
GigabitEthernet0/0     unassigned      YES unset  administratively down down 
GigabitEthernet0/1     unassigned      YES unset  up                    up 
GigabitEthernet0/1.10  192.168.10.1    YES manual up                    up 
GigabitEthernet0/1.20  192.168.20.1    YES manual up                    up 
GigabitEthernet0/1.30  192.168.30.1    YES manual up                    up 
GigabitEthernet0/1.1000unassigned      YES unset  up                    up 
GigabitEthernet0/2     unassigned      YES unset  administratively down down 
Vlan1                  unassigned      YES unset  administratively down down
R1#
```
интерфейсы включены и имеют адреса


### Часть 5. Проверьте, работает ли маршрутизация между VLAN

#### Шаг 1. Выполните следующие тесты с PC-A. Все должно быть успешно.

Примечание. Возможно, вам придется отключить брандмауэр ПК для работы ping

a.	Отправьте эхо-запрос с PC-A на шлюз по умолчанию.

```
Cisco Packet Tracer PC Command Line 1.0
C:\>ping 192.168.20.1

Pinging 192.168.20.1 with 32 bytes of data:

Request timed out.
Reply from 192.168.20.1: bytes=32 time<1ms TTL=255
Reply from 192.168.20.1: bytes=32 time<1ms TTL=255
Reply from 192.168.20.1: bytes=32 time<1ms TTL=255

Ping statistics for 192.168.20.1:
    Packets: Sent = 4, Received = 3, Lost = 1 (25% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms

C:\>
```

b.	Отправьте эхо-запрос с PC-A на PC-B.

```
C:\>ping 192.168.30.3

Pinging 192.168.30.3 with 32 bytes of data:

Reply from 192.168.30.3: bytes=32 time=1ms TTL=127
Reply from 192.168.30.3: bytes=32 time<1ms TTL=127
Reply from 192.168.30.3: bytes=32 time=1ms TTL=127
Reply from 192.168.30.3: bytes=32 time<1ms TTL=127

Ping statistics for 192.168.30.3:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 1ms, Average = 0ms

C:\>
```

c.	Отправьте команду ping с компьютера PC-A на коммутатор S2.

```
C:\>ping 192.168.10.12

Pinging 192.168.10.12 with 32 bytes of data:

Request timed out.
Reply from 192.168.10.12: bytes=32 time<1ms TTL=254
Reply from 192.168.10.12: bytes=32 time=1ms TTL=254
Reply from 192.168.10.12: bytes=32 time<1ms TTL=254

Ping statistics for 192.168.10.12:
    Packets: Sent = 4, Received = 3, Lost = 1 (25% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 1ms, Average = 0ms

C:\>

```

#### Шаг 2. Пройдите следующий тест с PC-B

В окне командной строки на PC-B выполните команду tracert на адрес PC-A.

```

C:\>tracert 192.168.20.3

Tracing route to 192.168.20.3 over a maximum of 30 hops: 

  1   0 ms      0 ms      0 ms      192.168.30.1
  2   0 ms      0 ms      1 ms      192.168.20.3

Trace complete.

C:\>
```

__Вопрос:__
Какие промежуточные IP-адреса отображаются в результатах?


В результате отобразились адрес маршрутизатора и PC-A (конечного устройства) 
