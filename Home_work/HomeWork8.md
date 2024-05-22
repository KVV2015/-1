# Лабораторная работа. Реализация DHCPv4 

[![2024-05-22-18-11-07.png](https://i.postimg.cc/tChyRGRG/2024-05-22-18-11-07.png)](https://postimg.cc/tYC8DfVB)


### Таблица адресации

| **Устройство ** 	| **Интерфейс** 	| **IP адрес** 	| **Маска подсети** 	| **Шлюз по умолчанию** 	|
|----------------:	|--------------:	|-------------:	|------------------:	|----------------------:	|
| R1              	| G0/0          	| 10.0.0.1     	| 255.255.255.252   	|          -             	|
|                 	| G0/1          	|   -      	     |                -    	|                       	|
|                 	| G0/1.100      	| 192.168.1.1  	| 255.255.255.0       	|                       	|
|                 	| G0/1.200      	| 192.168.2.1   	| 255.255.255.0     	|                       	|
|                 	| G0/1.1000     	|          -   	|     -              	|                       	|
| R2              	| G0/0          	| 10.0.0.2     	| 255.255.255.252   	|     -                  	|
|                 	| G0/1          	| 192.168.3.1     	| 255.255.255.0      	|                       	|
| S1              	| VLAN 200      	| 192.168.2.2     	| 255.255.255.0        	| 10.0.0.1 	          |
| S2              	| VLAN 1        	| 192.168.3.3      	| 255.255.255.0         	| 10.0.0.2             	|
| PC-A            	| NIC           	| DHCP         	| DHCP              	| DHCP                  	|
| PC-B            	| NIC           	| DHCP         	| DHCP              	| DHCP                  	|


### Таблица VLAN

| **VLAN** 	| **ИМЯ**     	| **Назначенный интерфейс**  	|
|----------	|-------------	|----------------------------	|
| 1        	| Нет         	| S2: F0/18                  	|
| 100      	| Клиенты     	| S1: F0/6                   	|
| 200      	| Управление  	| S1: VLAN 200               	|
| 999      	| Parking_lot 	| S1: F0/1-4,F0/7-24, G0/1-2 	|
| 1000     	| Собственная 	| -                          	|


## Задачи

Часть 1. Создание сети и настройка основных параметров устройства

Часть 2. Настройка и проверка двух серверов DHCPv4 на R1

Часть 3. Настройка и проверка DHCP-ретрансляции на R2

## Общие сведения 

Протокол динамической конфигурации сетевого узла (DHCP) — сетевой протокол, позволяющий сетевым администраторам управлять и автоматизировать назначение IP-адресов. Без использования DHCP  для IPv4 администратору необходимо вручную назначать и настраивать IP-адреса, предпочтительные DNS-серверы и шлюзы по умолчанию. По мере увеличения сети и перемещении устройств из одной внутренней сети в другую это становится административной проблемой.

В предложенном сценарии размеры компании увеличились, и сетевые администраторы больше не имеют возможности назначать IP-адреса для устройств вручную. Ваша задача заключается в настройке маршрутизатора R1 для назначения IPv4-адресов в двух разных подсетях. 

## Инструкция

### Часть 1.	Создание сети и настройка основных параметров устройства

В первой части лабораторной работы вам предстоит создать топологию сети и настроить базовые параметры для узлов ПК и коммутаторов.

#### Шаг 1.	Создание схемы адресации

Подсеть сети __192.168.1.0/24__ в соответствии со следующими требованиями:

__a__.	Одна подсеть __«Подсеть A»__, поддерживающая 58 хостов (клиентская VLAN 100 на R1).

Подсеть A: 192.168.1.1 - 192.168.1.59 / 255.255.255.0

Запишите первый IP-адрес в таблице адресации для R1 G0/1.100 .

__b__.	Одна подсеть __«Подсеть B»__, поддерживающая 28 хостов (управляющая VLAN 200 на R1). 

Подсеть B: 192.168.2.1 - 192.168.1.29 / 255.255.255.0

Запишите первый IP-адрес в таблице адресации для R1 G0/0/1.200. Запишите второй IP-адрес в таблице адресов для S1 VLAN 200 и введите соответствующий шлюз по умолчанию.

__c__.	Одна подсеть __«Подсеть C»__, поддерживающая 12 узлов (VLAN 100 на R2).

Подсеть C: 192.168.1.1 - 192.168.1.12 / 255.255.255.0

Запишите первый IP-адрес в таблице адресации для R2 G0/1.


#### Шаг 2. Создание схемы адресации

Подключите устройства, как показано в топологии, и подсоедините необходимые кабели.

#### Шаг 3. Проведем базовую настройку маршрутизаторов.

a.	Назначьте маршрутизатору имя устройства.

Откройте окно конфигурации

b.	Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.

c.	Назначьте class в качестве зашифрованного пароля привилегированного режима EXEC.

d.	Назначьте cisco в качестве пароля консоли и включите вход в систему по паролю.

e.	Назначьте cisco в качестве пароля VTY и включите вход в систему по паролю.

f.	Зашифруйте открытые пароли.

g.	Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.

h.	Сохраните текущую конфигурацию в файл загрузочной конфигурации.

i.	Установите часы на маршрутизаторе на сегодняшнее время и дату.

__Примечание__. Вопросительный знак (?) позволяет открыть справку с правильной последовательностью параметров, необходимых для выполнения этой команды.

#### Шаг 4.	Настройка маршрутизации между сетями VLAN на маршрутизаторе R1

a.	Активируйте интерфейс G0/1 на маршрутизаторе.

b.	Настройте подинтерфейсы для каждой VLAN в соответствии с требованиями таблицы IP-адресации. Все субинтерфейсы используют инкапсуляцию 802.1Q и назначаются первый полезный адрес из вычисленного пула IP-адресов. Убедитесь, что подинтерфейсу для native VLAN не назначен IP-адрес. Включите описание для каждого подинтерфейса.

```
R1(config)#
R1(config)#int g0/1.100
R1(config-subif)#
%LINK-5-CHANGED: Interface GigabitEthernet0/1.100, changed state to up

R1(config-subif)#
R1(config-subif)#encapsulation dot1Q 100
R1(config-subif)#
R1(config-subif)#ip add
R1(config-subif)#ip address 192.168.1.1 255.255.255.0
R1(config-subif)#
R1(config-subif)#description default gateway for vlan 100
R1(config-subif)#no sh
R1(config-subif)#ex
R1(config)#
R1(config)#int g0/1.200
R1(config-subif)#
R1(config-subif)#encapsulation dot1Q 200
R1(config-subif)#
R1(config-subif)#ip address 192.168.2.2 255.255.255.0
R1(config-subif)#
R1(config-subif)#description default gateway for vlan 200
R1(config-subif)#no sh
R1(config-subif)#ex
R1(config)#
R1(config)#int g0/1.1000
R1(config-subif)#
%LINK-5-CHANGED: Interface GigabitEthernet0/1.1000, changed state to up

R1(config-subif)#
R1(config-subif)#encapsulation dot1Q 1000 Native
R1(config-subif)#
R1(config-subif)#description default gateway for vlan 1000
R1(config-subif)#
R1(config-subif)#ex
R1(config)#
R1(config)#int g0/1
R1(config-if)#
R1(config-if)#description trunk link to S1
R1(config-if)#
R1(config-if)#no sh
R1(config-if)#
```

c.	Убедитесь, что вспомогательные интерфейсы работают.

```
R1#sh ip route 
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2, E - EGP
       i - IS-IS, L1 - IS-IS level-1, L2 - IS-IS level-2, ia - IS-IS inter area
       * - candidate default, U - per-user static route, o - ODR
       P - periodic downloaded static route

Gateway of last resort is not set

     192.168.1.0/24 is variably subnetted, 2 subnets, 2 masks
C       192.168.1.0/24 is directly connected, GigabitEthernet0/1.100
L       192.168.1.1/32 is directly connected, GigabitEthernet0/1.100
     192.168.2.0/24 is variably subnetted, 2 subnets, 2 masks
C       192.168.2.0/24 is directly connected, GigabitEthernet0/1.200
L       192.168.2.2/32 is directly connected, GigabitEthernet0/1.200

R1#
R1#
R1#sh ip int br
Interface              IP-Address      OK? Method Status                Protocol 
GigabitEthernet0/0     unassigned      YES unset  administratively down down 
GigabitEthernet0/1     unassigned      YES unset  up                    up 
GigabitEthernet0/1.100 192.168.1.1     YES manual up                    up 
GigabitEthernet0/1.200 192.168.2.2     YES manual up                    up 
GigabitEthernet0/1.1000unassigned      YES unset  up                    up 
GigabitEthernet0/2     unassigned      YES unset  administratively down down 
Vlan1                  unassigned      YES unset  administratively down down
R1#
```

#### Шаг 5. Настройте G0/1 на R2, затем G0/0 и статическую маршрутизацию для обоих маршрутизаторов

a.	Настройте G0/1 на R2 с первым IP-адресом подсети C, рассчитанным ранее.

```
R2(config)#
R2(config)#int g0/1
R2(config-if)#
R2(config-if)#ip add
R2(config-if)#ip address 192.168.3.1 255.255.255.0
R2(config-if)#
R2(config-if)#ex
R2(config)#
R2(config-if)#
R2(config-if)#no sh

R2(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet0/1, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/1, changed state to up

R2(config-if)#
```

b.	Настройте интерфейс G0/0 для каждого маршрутизатора на основе приведенной выше таблицы IP-адресации.

```
R2(config-if)#int g0/0
R2(config-if)#
R2(config-if)#ip add 10.0.0.2 255.255.255.252
R2(config-if)#
```

c.	Настройте маршрут по умолчанию на каждом маршрутизаторе, указываемом на IP-адрес G0/0 на другом маршрутизаторе.

Для роутера 1 шлюзом по умолчанию будет роутер 2 и IP address порта смотрящего на роутер 1. Для настройки статической маршрутизации вводим команду __ip route__ указывая следующий путь: 

адрес + маска сети на int g0/1 (которая смотрит на хосты) + адрес  входного int g0/0

```
R(config)#
R1(config)#ip route 192.168.3.0 255.255.255.252 10.0.0.2
R1(config)#
```

d.	Убедитесь, что статическая маршрутизация работает с помощью пинга до адреса G0/1 R2 от R1.

```
R1#
R1#ping 192.168.3.1

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.3.1, timeout is 2 seconds:
.!!!!
Success rate is 80 percent (4/5), round-trip min/avg/max = 0/0/0 ms

R1#ping 192.168.3.1

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.3.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 0/0/0 ms

```

e.	Сохраните текущую конфигурацию в файл загрузочной конфигурации

```
R1#wr
Building configuration...
[OK]
R1#sh run
Building configuration...

Current configuration : 1144 bytes
!
version 15.1
no service timestamps log datetime msec
no service timestamps debug datetime msec
no service password-encryption
!
hostname R1
!
!
!
ip cef
no ipv6 cef
!

license udi pid CISCO2911/K9 sn FTX1524PNVA-
!
!
spanning-tree mode pvst
!
!!
interface GigabitEthernet0/0
 ip address 10.0.0.1 255.255.255.252
 duplex auto
 speed auto
!
interface GigabitEthernet0/1
 no ip address
 duplex auto
 speed auto
!
interface GigabitEthernet0/1.100
 description default gateway for vlan 100
 encapsulation dot1Q 100
 ip address 192.168.1.1 255.255.255.0
!
interface GigabitEthernet0/1.200
 description default gateway for vlan 200
 encapsulation dot1Q 200
 ip address 192.168.2.1 255.255.255.0
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
!
ip classless
ip route 192.168.3.0 255.255.255.0 10.0.0.2 
!
ip flow-export version 9
!

!
end


R1#
```

Так же настраиваем маршрутизацию между R2 -R1, указав сеть интерфеса g0/1 с ее маской + входной порт роутера 1.

```
R2(config)#
R2(config)#ip route 192.168.1.0 255.255.255.0 10.0.0.1
R2(config)#
!
R2#sh run
Building configuration...
!
ip classless
ip route 192.168.1.0 255.255.255.0 10.0.0.1 
!
```

#### Шаг 6.	Настройте базовые параметры каждого коммутатора.

a.	Присвойте коммутатору имя устройства.

Откройте окно конфигурации

b.	Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.

c.	Назначьте class в качестве зашифрованного пароля привилегированного режима EXEC.

d.	Назначьте cisco в качестве пароля консоли и включите вход в систему по паролю.

e.	Назначьте cisco в качестве пароля VTY и включите вход в систему по паролю.

f.	Зашифруйте открытые пароли.

g.	Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.

h.	Сохраните текущую конфигурацию в файл загрузочной конфигурации.

i.	Установите часы на сегодняшнее время и дату.

__Примечание__. Вопросительный знак (?) позволяет открыть справку с правильной последовательностью параметров, необходимых для выполнения этой команды.

j.	Скопируйте текущую конфигурацию в файл загрузочной конфигурации.

```
Switch>
Switch>en
Switch#
Switch#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#
Switch(config)#Host S1
S1(config)#
S1(config)#
S1(config)#no ip domain-l
S1(config)#no ip domain-lookup 
S1(config)#
S1(config)#enable pass
S1(config)#enable password class
S1(config)#
S1(config)#line con 0
S1(config)#line con 0
S1(config-line)#
S1(config-line)#pass cisco
S1(config-line)#log
% Ambiguous command: "log"
S1(config-line)#login
S1(config-line)#
S1(config-line)#exit
S1(config)#line vty 0 4
S1(config-line)#
S1(config-line)#pass cisco
S1(config-line)#
S1(config-line)#login
S1(config-line)#
S1(config-line)#exit
S1(config)#
S1(config)#serv
S1(config)#service pass
S1(config)#service password-encryption 
S1(config)#
S1(config)#banner motd @++++DANGER++++@
S1(config)#
S1(config)#do wr
Building configuration...
[OK]
S1(config)#
S1(config)#^Z
S1#
%SYS-5-CONFIG_I: Configured from console by console

S1#
S1#clock set 10:57:10 22 may 2024
S1#
S1#wr
Building configuration...
[OK]
S1#
S1#
```

#### Шаг 7. Создайте сети VLAN на коммутаторе S1.

__Примечание__. S2 настроен только с базовыми настройками. 

a.	Создайте необходимые VLAN на коммутаторе S1 и присвойте им имена из приведенной выше таблицы.
```
S1(config)#
S1(config)#vlan 100
S1(config-vlan)#
S1(config-vlan)#name Clients
S1(config-vlan)#
S1(config-vlan)#vlan 200 
S1(config-vlan)#
S1(config-vlan)#name Upravlenye
S1(config-vlan)#
S1(config-vlan)#vlan 999
S1(config-vlan)#name Parking_Lot
S1(config-vlan)#
S1(config-vlan)#vlan 1000
S1(config-vlan)#
S1(config-vlan)#name Native
S1(config-vlan)#
```

b.	Настройте и активируйте интерфейс управления на S1 (VLAN 200), используя второй IP-адрес из подсети, рассчитанный ранее. Кроме того установите шлюз по умолчанию на S1.

```
S1(config)#
S1(config)#int vlan 200
S1(config-if)#
%LINK-5-CHANGED: Interface Vlan200, changed state to up

S1(config-if)#
S1(config-if)#ip address 192.168.2.2 255.255.255.0
S1(config-if)#
S1(config-if)#description default gateway for 10.0.0.1
S1(config-if)#
S1(config-if)#no sh
S1(config-if)#
S1(config-if)#^Z
S1#
%SYS-5-CONFIG_I: Configured from console by console

S1#
S1#wr
Building configuration...
[OK]
S1#
```
c.	Настройте и активируйте интерфейс управления на S2 (VLAN 1), используя второй IP-адрес из подсети, рассчитанный ранее. Кроме того, установите шлюз по умолчанию на S2

```
2(config)#
S2(config)#int vlan 1
S2(config-if)#
S2(config-if)#ip add
S2(config-if)#ip address 192.168.3.1 255.255.255.0
S2(config-if)#
S2(config-if)#description default gateway for 10.0.0.2
S2(config-if)#
S2(config-if)#ex
S2(config)#
S2(config)#do wr
Building configuration...
[OK]
```

d.	Назначьте все неиспользуемые порты S1 VLAN Parking_Lot, настройте их для статического режима доступа и административно деактивируйте их. 

На S2 административно деактивируйте все неиспользуемые порты.

Для коммутатора S1:

```
S1(config)#
S1(config)#int range f0/1-4,f0/7-24,g0/1-2
S1(config-if-range)#
S1(config-if-range)#switch
S1(config-if-range)#switchport acc
S1(config-if-range)#switchport mode acc
S1(config-if-range)#switchport mode access 
S1(config-if-range)#
S1(config-if-range)#switch
S1(config-if-range)#switchport acc
S1(config-if-range)#switchport access vlan 999
S1(config-if-range)#
S1(config-if-range)#sh
```

Для коммутатора S2:

```
S2(config)#
S2(config)#int range f0/1-4,f0/6-17,f0/19-24,g0/1-2
S2(config-if-range)#
S2(config-if-range)#sh

```

__Примечание__. Команда interface range полезна для выполнения этой задачи с минимальным количеством команд.


#### Шаг 8. Назначьте сети VLAN соответствующим интерфейсам коммутатора.

a.	Назначьте используемые порты соответствующей VLAN (указанной в таблице VLAN выше) и настройте их для режима статического доступа.
Откройте окно конфигурации
```
S1(config)#
S1(config)#int f0/5
S1(config-if)#
S1(config-if)#switchport mode access 
S1(config-if)#
S1(config-if)#switchport acc
S1(config-if)#switchport access vlan 200
S1(config-if)#
S1(config-if)#
%LINEPROTO-5-UPDOWN: Line protocol on Interface Vlan200, changed state to up

S1(config-if)#
S1(config-if)#ex
S1(config)#
S1(config)#int f0/6
S1(config-if)#
S1(config-if)#switchport mode access 
S1(config-if)#
S1(config-if)#switchport access vlan 100
S1(config-if)#
S1(config-if)#ex
S1(config)# 
```


b.	Убедитесь, что VLAN назначены на правильные интерфейсы.


```
S1#
S1#sh vlan

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    
100  Clients                          active    Fa0/6
200  Upravlenye                       active    Fa0/5
999  Parking_Lot                      active    Fa0/1, Fa0/2, Fa0/3, Fa0/4
                                                Fa0/7, Fa0/8, Fa0/9, Fa0/10
                                                Fa0/11, Fa0/12, Fa0/13, Fa0/14
                                                Fa0/15, Fa0/16, Fa0/17, Fa0/18
                                                Fa0/19, Fa0/20, Fa0/21, Fa0/22
                                                Fa0/23, Fa0/24, Gig0/1, Gig0/2
1000 Native                           active    
1002 fddi-default                     active    
1003 token-ring-default               active    
1004 fddinet-default                  active    
1005 trnet-default                    active   
```


```
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
 switchport access vlan 999
 switchport mode access
 shutdown
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
 switchport access vlan 200
 switchport mode access
!
interface FastEthernet0/6
 switchport access vlan 100
 switchport mode access
!
interface FastEthernet0/7
 switchport access vlan 999
 switchport mode access
 shutdown
!
!
interface Vlan1
 no ip address
 shutdown
!
interface Vlan 200
 description default gateway for 10.0.0.1
 ip address 192.168.2.2 255.255.255.0
!
```

#### Шаг 9. Вручную настройте интерфейс S1 F0/5 в качестве транка 802.1Q.

a.	Измените режим порта коммутатора, чтобы принудительно создать магистральный канал.

```
S1(config)#
S1(config)#int fa0/5
S1(config-if)#
S1(config-if)#swit
S1(config-if)#switchport mode trunk

S1(config-if)#
%LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/5, changed state to down

%LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/5, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface Vlan200, changed state to up

S1(config-if)#

```

b.	В рамках конфигурации транка  установите для native  VLAN значение 1000.

```
S1(config-if)#
S1(config-if)#switchport trunk native vlan
S1(config-if)#switchport trunk native vlan 1000
S1(config-if)#ex
S1(config)#
```


c.	В качестве другой части конфигурации магистрали укажите, что VLAN 100, 200 и 1000 могут проходить по транку.

```
S1(config-if)#switc
S1(config-if)#switchport trunk all
S1(config-if)#switchport trunk allowed vlan 100,200,1000
S1(config-if)#
```

d.	Сохраните текущую конфигурацию в файл загрузочной конфигурации.

e.	Проверьте состояние транка.

```
S1(config-if)#do wr
Building configuration...
[OK]
S1(config-if)#
S1(config-if)#^Z
S1#
%SYS-5-CONFIG_I: Configured from console by console

S1#
S1#
S1#sh int tr
Port        Mode         Encapsulation  Status        Native vlan
Fa0/5       on           802.1q         trunking      1000

Port        Vlans allowed on trunk
Fa0/5       100,200,1000

Port        Vlans allowed and active in management domain
Fa0/5       100,200,1000

Port        Vlans in spanning tree forwarding state and not pruned
Fa0/5       100,200,1000

S1#
```

__Вопрос__:
Какой IP-адрес был бы у ПК, если бы он был подключен к сети с помощью DHCP?

### Часть 2.	Настройка и проверка двух серверов DHCPv4 на R1

В части 2 необходимо настроить и проверить сервер DHCPv4 на R1. Сервер DHCPv4 будет обслуживать две подсети, __Подсеть "A"__ и __Подсеть "C"__.

#### Шаг 1. Настройте R1 с пулами DHCPv4 для двух поддерживаемых подсетей. Ниже приведен только пул DHCP для подсети "A"

a.	Исключите первые пять используемых адресов из каждого пула адресов.

```
R1#
R1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#
R1(config)#ip dhcp excluded-address 192.168.1.1
R1(config)#
R1(config)#ip dhcp excluded-address 192.168.1.2 192.168.1.5
R1(config)#
```


b.	Создайте пул DHCP (используйте уникальное имя для каждого пула).

```
R1(config)#
R1(config)#ip dhcp pool R1_Client_LAN
```

c. Укажите сеть, поддерживающую этот DHCP-сервер.

```
R1(dhcp-config)#
R1(dhcp-config)#network 192.168.1.0 255.255.255.0
R1(dhcp-config)#
```

d. В качестве имени домена укажите __CCNA-lab.com__.

```
R1(dhcp-config)#
R1(dhcp-config)#domain-name CCNA-lab.com
R1(dhcp-config)#
```

e. Настройте соответствующий шлюз по умолчанию для каждого пула DHCP.

```
R1(dhcp-config)#
R1(dhcp-config)#default-router 192.168.1.1
R1(dhcp-config)#
```

f.	Настройте время аренды на 2 дня 12 часов и 30 минут.

```
R1(config)#ip dhcp pool Lan_pool1
R1(dhcp-config)#
R1(dhcp-config)#lease 02 13 30
                ^
% Invalid input detected at '^' marker.
	
```
Команда __lease__ выдает ошибку !!! __Из-за чего?___


g.	Затем настройте второй пул DHCPv4, используя имя пула 
R2_Client_LAN и вычислите сеть, маршрутизатор по умолчанию, и используйте то же имя домена и время аренды, что и предыдущий пул DHCP.

- Создайте пул DHCP, использовав уникальное имя __R2_Client_LAN__.
- Укажите сеть, поддерживающую этот DHCP-сервер.
- В качестве имени домена укажите __CCNA-lab.com__.
- Настройте соответствующий шлюз по умолчанию для пула.
- Настройте время аренды на 2 дня 12 часов и 30 минут

```
R1(config)#ip dhcp pool R2_Client_LAN
R1(dhcp-config)#
R1(dhcp-config)#ip add
R1(dhcp-config)#network 192.168.3.0 255.255.255.0
R1(dhcp-config)#
R1(dhcp-config)#domain-name CCNA-lab.com
R1(dhcp-config)#
R1(dhcp-config)#default-router 192.168.3.1
R1(dhcp-config)#
R1(dhcp-config)#lease 02 12 30
                ^
% Invalid input detected at '^' marker.
	
R1(dhcp-config)#

```

#### Шаг 2.	Сохраните конфигурацию.

```
R1#sh run
Building configuration...

Current configuration : 1545 bytes
!
version 15.1
no service timestamps log datetime msec
no service timestamps debug datetime msec
no service password-encryption
!
hostname R1
!
!
!
!
ip dhcp excluded-address 192.168.1.1
ip dhcp excluded-address 192.168.1.2 192.168.1.5
ip dhcp excluded-address 192.168.3.1
ip dhcp excluded-address 192.168.3.2 192.168.3.6
!
ip dhcp pool R2_Client_LAN
 network 192.168.3.0 255.255.255.0
 default-router 192.168.3.1
 domain-name CCNA-lab.com
ip dhcp pool R1_Client_LAN
 network 192.168.1.0 255.255.255.0
 default-router 192.168.1.1
 domain-name CCNA-lab.com
!
!
!
ip cef
no ipv6 cef
!
!
!
!
license udi pid CISCO2911/K9 sn FTX1524PNVA-
!
!
!
!
!
!
!
!
!
!
!
spanning-tree mode pvst
!
!
!
!
!
!
interface GigabitEthernet0/0
 ip address 10.0.0.1 255.255.255.252
 duplex auto
 speed auto
!
interface GigabitEthernet0/1
 no ip address
 duplex auto
 speed auto
!
interface GigabitEthernet0/1.100
 description default gateway for vlan 100
 encapsulation dot1Q 100
 ip address 192.168.1.1 255.255.255.0
!
interface GigabitEthernet0/1.200
 description default gateway for vlan 200
 encapsulation dot1Q 200
 ip address 192.168.2.1 255.255.255.0
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
!
ip classless
ip route 192.168.3.0 255.255.255.0 10.0.0.2 
!
ip flow-export version 9
!
!
!
!
!
!
!
!
line con 0
!
line aux 0
!
line vty 0 4
 login
!
!
!
end


R1#
```

#### Шаг 3. Проверка конфигурации сервера DHCPv4

a.	Чтобы просмотреть сведения о пуле, выполните команду __show ip dhcp pool__.

```
R1#show ip dhcp pool

Pool R2_Client_LAN :
 Utilization mark (high/low)    : 100 / 0
 Subnet size (first/next)       : 0 / 0 
 Total addresses                : 254
 Leased addresses               : 0
 Excluded addresses             : 4
 Pending event                  : none

 1 subnet is currently in the pool
 Current index        IP address range                    Leased/Excluded/Total
 192.168.3.1          192.168.3.1      - 192.168.3.254     0    / 4     / 254

Pool R1_Client_LAN :
 Utilization mark (high/low)    : 100 / 0
 Subnet size (first/next)       : 0 / 0 
 Total addresses                : 254
 Leased addresses               : 0
 Excluded addresses             : 4
 Pending event                  : none

 1 subnet is currently in the pool
 Current index        IP address range                    Leased/Excluded/Total
 192.168.1.1          192.168.1.1      - 192.168.1.254     0    / 4     / 254
R1#
```

b.	Выполните команду __show ip dhcp binding__ для проверки установленных назначений адресов DHCP.
```
1#show ip dhcp binding

IP address       Client-ID/              Lease expiration        Type
                 Hardware address
R1#
```

c.	Выполните команду ___show ip dhcp server statistics__ для проверки сообщений DHCP

```
не поддерживается в маршрутизаторе
```

#### Шаг 4. Попытка получить IP-адрес от DHCP на PC-A

a.	Из командной строки компьютера PC-A выполните команду __ipconfig /all__

```
C:\>ipconfig /all

FastEthernet0 Connection:(default port)

   Connection-specific DNS Suffix..: CCNA-lab.com
   Physical Address................: 0060.2F58.4EC1
   Link-local IPv6 Address.........: FE80::260:2FFF:FE58:4EC1
   IPv6 Address....................: ::
   IPv4 Address....................: 192.168.1.6
   Subnet Mask.....................: 255.255.255.0
   Default Gateway.................: ::
                                     192.168.1.1
   DHCP Servers....................: 192.168.1.1
   DHCPv6 IAID.....................: 
   DHCPv6 Client DUID..............: 00-01-00-01-02-1E-7D-CE-00-60-2F-58-4E-C1
   DNS Servers.....................: ::
                                     0.0.0.0

```

b.	После завершения процесса обновления выполните команду __ipconfig__ для просмотра новой информации об IP-адресе.

```
C:\>ipconfig

FastEthernet0 Connection:(default port)

   Connection-specific DNS Suffix..: CCNA-lab.com
   Link-local IPv6 Address.........: FE80::260:2FFF:FE58:4EC1
   IPv6 Address....................: ::
   IPv4 Address....................: 192.168.1.6
   Subnet Mask.....................: 255.255.255.0
   Default Gateway.................: ::
                                     192.168.1.1

```

c.	Проверьте подключение с помощью пинга IP-адреса интерфейса R1 G0/1.

```
C:\>ping 192.168.1.1

Pinging 192.168.1.1 with 32 bytes of data:

Reply from 192.168.1.1: bytes=32 time<1ms TTL=255
Reply from 192.168.1.1: bytes=32 time<1ms TTL=255
Reply from 192.168.1.1: bytes=32 time=1ms TTL=255
Reply from 192.168.1.1: bytes=32 time<1ms TTL=255

Ping statistics for 192.168.1.1:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 1ms, Average = 0ms

C:\>
```

### Часть 3. Настройка и проверка DHCP-ретрансляции на R2.

В части 3 настраивается R2 для ретрансляции DHCP-запросов из локальной сети на интерфейсе G0/1 на DHCP-сервер (R1). 

#### Шаг 1. Настройка R2 в качестве агента DHCP-ретрансляции для локальной сети на G0/1

a.	Настройте команду __ip helper-address__ на G0/1, указав IP-адрес G0/0 R1.

Откройте окно конфигурации

```
2(config)#
R2(config)#int g0/1
R2(config-if)#
R2(config-if)#ip helper-address 10.0.0.1
R2(config-if)#
R2(config-if)#ex
R2(config)#
R2(config)#do wr
Building configuration...
[OK]
R2(config)#
```
- Сохраним загрузочную конфигупацию:
```

R2#  
R2#sh run
Building configuration...

Current configuration : 760 bytes
!
version 15.1
no service timestamps log datetime msec
no service timestamps debug datetime msec
no service password-encryption
!
hostname R2
!
!
ip cef
no ipv6 cef
!
license udi pid CISCO2911/K9 sn FTX1524GHAU-
!
!
no ip domain-lookup
!
!
spanning-tree mode pvst
!
!
interface GigabitEthernet0/0
 ip address 10.0.0.2 255.255.255.252
 duplex auto
 speed auto
!
interface GigabitEthernet0/1
 ip address 192.168.3.1 255.255.255.0
 ip helper-address 10.0.0.1
 duplex auto
 speed auto
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
!
!
end


R2#
```

#### Шаг 2. Попытка получить IP-адрес от DHCP на PC-B

a.	Из командной строки компьютера PC-B выполните команду __ipconfig /all__

```
C:\>ipconfig /all

FastEthernet0 Connection:(default port)

   Connection-specific DNS Suffix..: CCNA-lab.com
   Physical Address................: 0001.964D.04A0
   Link-local IPv6 Address.........: FE80::201:96FF:FE4D:4A0
   IPv6 Address....................: ::
   IPv4 Address....................: 0.0.0.0
   Subnet Mask.....................: 0.0.0.0
   Default Gateway.................: ::
                                     0.0.0.0
   DHCP Servers....................: 10.0.0.1
   DHCPv6 IAID.....................: 
   DHCPv6 Client DUID..............: 00-01-00-01-65-64-C7-09-00-01-96-4D-04-A0
   DNS Servers.....................: ::
                                     0.0.0.0

```

b.	Установите флажок настроек на получение DHCP. После завершения процесса обновления выполните команду __ipconfig__ для просмотра новой информации об IP-адресе.

```
C:\>ipconfig

FastEthernet0 Connection:(default port)

   Connection-specific DNS Suffix..: CCNA-lab.com
   Link-local IPv6 Address.........: FE80::201:96FF:FE4D:4A0
   IPv6 Address....................: ::
   IPv4 Address....................: 192.168.3.7
   Subnet Mask.....................: 255.255.255.0
   Default Gateway.................: ::
                                     192.168.3.1

```

c.	Проверьте подключение с помощью пинга IP-адреса интерфейса R1 G0/1.

```
C:\>ping 192.168.1.1

Pinging 192.168.1.1 with 32 bytes of data:

Reply from 192.168.1.1: bytes=32 time<1ms TTL=254
Reply from 192.168.1.1: bytes=32 time<1ms TTL=254
Reply from 192.168.1.1: bytes=32 time<1ms TTL=254
Reply from 192.168.1.1: bytes=32 time<1ms TTL=254

Ping statistics for 192.168.1.1:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms

C:\>
```

d.	Выполните __show ip dhcp binding__ для R1 для проверки назначений адресов в DHCP.

```
R1#
R1#show ip dhcp binding
IP address       Client-ID/              Lease expiration        Type
                 Hardware address
192.168.3.7      0001.964D.04A0           --                     Automatic
192.168.1.6      0060.2F58.4EC1           --                     Automatic
R1#
```

e.	Выполните команду __show ip dhcp server statistics__ для проверки сообщений DHCP.

```
R1#
R1#show ip dhcp server statistics
                ^
% Invalid input detected at '^' marker.
	
R1#
```

Видимо не поддерживается на роутере !
