#  Настройка IPv6-адресов на сетевых устройствах 

------------

[![2024-04-02-17-56-09.png](https://i.postimg.cc/yd4vg9xq/2024-04-02-17-56-09.png)](https://postimg.cc/2qG4g1nT)

#### Таблица адресации
|    <br>Устройство    	|    <br>Интерфейс    	|    <br>IPv6-адрес             	|    <br>Длина   префикса    	|    <br>Шлюз   по умолчанию    	|
|----------------------	|---------------------	|-------------------------------	|----------------------------	|-------------------------------	|
|    <br>R1            	|    <br>G0/0         	|    <br>2001:db8:acad:a::1     	|    <br>64                  	|    <br>—                      	|
|    <br>R1            	|    <br>G0/1         	|    <br>2001:db8:acad:1::1     	|    <br>64                  	|    <br>—                      	|
|    <br>S1            	|    <br>VLAN 1       	|    <br>2001:db8:acad:1::b     	|    <br>64                  	|    <br>—                      	|
|    <br>PC-A          	|    <br>NIC          	|    <br>2001:db8:acad:1::3     	|    <br>64                  	|    <br>fe80::1                	|
|    <br>PC-B          	|    <br>NIC          	|    <br>2001:db8:acad:a::3     	|    <br>64                  	|    <br>fe80::1                	|



##  Задание:
### **Часть 1. Настройка топологии и конфигурация основных параметров маршрутизатора и коммутатора**


### **Часть 2. Ручная настройка IPv6-адресов**

### **Часть 3. Проверка сквозного соединения**

### Общие сведения
В данной лабораторной работе  нам предстоит настраивать хосты и интерфейсы устройств с IPv6-адресами.  Для просмотра индивидуальных и групповых IPv6-адресов будем использовать команду show. После настройки проерим сквозное соединение с помощью команд ping and traceroute.

__Примечание:__  Убедитесь, что у всех маршрутизаторов и коммутаторов была удалена начальная конфигурация.

__Примечание.__ Шаблон по умолчанию менеджера базы данных 2960 Switch Database Manager (SDM) не поддерживает IPv6. Перед назначением IPv6-адреса SVI VLAN 1 может понадобиться выполнение команды __sdm prefer dual-ipv4-and-ipv6 default__ для включения IPv6-адресации.

__Примечание.__ Шаблон __default bias__, который по умолчанию используется диспетчером SDM (диспетчер базы данных коммутатора), не предоставляет возможностей адресации IPv6. Убедитесь, что SDM использует шаблон __dual-ipv4-and-ipv6__ или __lanbase-routing__. Новый шаблон будет использоваться после перезагрузки. 

```
S1# show sdm prefer
```
Чтобы установить шаблон __dual-ipv4-and-ipv6__ в качестве шаблона SDM по умолчанию, выполните следующие действия:

```
S1# configure terminal
S1(config)# sdm prefer dual-ipv4-and-ipv6 default
S1(config)# end
S1# reload
```


### Инструкции

### **Часть 1. Настройка топологии и конфигурация основных параметров маршрутизатора и коммутатора**

После подключения сети, инициализации и перезагрузки маршрутизатора и коммутатора выполняем следующие действия:

#### **Шаг 1. Настройка маршрутизатора**

Настроим имя хоста и основные параметры устройства:

```
Router>
Router>en
Router#
Router#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Router(config)#
Router(config)#hostname R1
R1(config)#
R1(config)#
R1(config)#service password-encryption
R1(config)#
R1(config)#enable secr
R1(config)#enable secret class
R1(config)#
R1(config)#banner motd # DANGER #
R1(config)#
```
#### **Шаг 2. Настройка коммутатора**

Настроим имя хоста и основные параметры устройства:

```
Switch>
Switch>en
Switch#
Switch#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#
Switch(config)#host S1
S1(config)#
S1(config)#service password-encryption
S1(config)#
S1(config)#enable secret class
S1(config)#
S1(config)#banner motd @ +++++ DANGER !! ++++@
S1(config)#
S1(config)#end
S1#
S1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
S1(config)#line vty 0 4
S1(config-line)#
S1(config-line)#pass
S1(config-line)#password class
S1(config-line)#
S1(config-line)#login
S1(config-line)#
S1(config-line)#end
S1#
```

### **Часть 2. Ручная настройка IPv6-адресов**

#### **Шаг 1. **Назначим IPv6-адреса интерфейсам Ethernet на R1.**

 a. Назначим глобальные индивидуальные IPv6-адреса, указанные в таблице адресации обоим интерфейсам Ethernet на R1:

```
R1#
R1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#
R1(config)#int g0/0
R1(config-if)#
R1(config-if)#ipv6 add
R1(config-if)#ipv6 address 2001:db8:acad:a::1/64
R1(config-if)#
R1(config-if)#no sh
R1(config-if)#no shutdown 

R1(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0, changed state to up

R1(config-if)#

R1(config-if)#
R1(config-if)#int g0/1
R1(config-if)#
R1(config-if)#ipv6 address 2001:db8:acad:1::1/64
R1(config-if)#
R1(config-if)#no shu
R1(config-if)#no shutdown 

R1(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet0/1, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/1, changed state to up

R1(config-if)#
```

b. Введем команду __show ipv6 interface brief__ чтобы проверить, назначен ли каждому интерфейсу корректный индивидуальный IPv6-адрес:

```
R1#sh ipv6 int br
R1#sh ipv6 int brief 
GigabitEthernet0/0         [up/up]
    FE80::201:43FF:FE6C:9A01
    2001:DB8:ACAD:A::1
GigabitEthernet0/1         [up/up]
    FE80::201:43FF:FE6C:9A02
    2001:DB8:ACAD:1::1
GigabitEthernet0/2         [administratively down/down]
    unassigned
Vlan1                      [administratively down/down]
    unassigned
R1#
```

c. Чтобы обеспечить соответствие локальных адресов канала индивидуальному адресу, вручную настроим локальные адреса канала на каждом интерфейсе Ethernet на R1:

```
R1(config)#
R1(config)#int g 0/0
R1(config-if)#
R1(config-if)#ipv6 add
R1(config-if)#ipv6 address fe80::1 l
R1(config-if)#ipv6 address fe80::1 link-local 
R1(config-if)#
R1(config-if)#no sh
R1(config-if)#end
R1#
%SYS-5-CONFIG_I: Configured from console by console

R1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#int g 0/1
R1(config-if)#
R1(config-if)#ipv6 address fe80::1 l
R1(config-if)#
R1(config-if)#no sh
R1(config-if)#
R1(config-if)#end
R1#
```
__Примечание.__ Каждый интерфейс маршрутизатора относится к отдельной сети. Пакеты с локальным адресом канала никогда не выходят за пределы локальной сети, а значит, для обоих интерфейсов можно указывать один и тот же локальный адрес канала.

d. Используя комманду __how ipv6 interface brief__ проверим изменился ли локальный адрес сети:

```
R1#
R1#show ipv6 interface brief
GigabitEthernet0/0         [up/up]
    FE80::1
    2001:DB8:ACAD:A::1
GigabitEthernet0/1         [up/up]
    FE80::1
    2001:DB8:ACAD:1::1
GigabitEthernet0/2         [administratively down/down]
    unassigned
Vlan1                      [administratively down/down]
    unassigned
R1#
```
e. Используя команду __Show ipv6 interface g0/0__ проверим к каким группам многоадресной рассылки относится интерфейс g0/0:

```
R1#show ipv6 interface g0/0
GigabitEthernet0/0 is up, line protocol is up
  IPv6 is enabled, link-local address is FE80::1
  No Virtual link-local address(es):
  Global unicast address(es):
    2001:DB8:ACAD:A::1, subnet is 2001:DB8:ACAD:A::/64
  Joined group address(es):
    FF02::1
    FF02::1:FF00:1
  MTU is 1500 bytes
  ICMP error messages limited to one every 100 milliseconds
  ICMP redirects are enabled
  ICMP unreachables are sent
  ND DAD is enabled, number of DAD attempts: 1
  ND reachable time is 30000 milliseconds
R1#
```
В данном скрипте мы видим, что данный роутер относится к группе на которую подсоеденины все узлы IPv6, но у нас нет группы маршрутизации, так как IPv6 маршрутизация на маршрутизаторах по умолчанию выключена.


#### **Шаг 2. Активируем IPv6-маршрутизацию на R1.**

a. В командной строке на PC-B введем команду ipconfig, чтобы получить данные IPv6-адреса, назначенного интерфейсу ПК:

```
C:\>ipconfig

FastEthernet0 Connection:(default port)

   Connection-specific DNS Suffix..: 
   Link-local IPv6 Address.........: FE80::2E0:F9FF:FE4D:298C
   IPv6 Address....................: ::
   IPv4 Address....................: 0.0.0.0
   Subnet Mask.....................: 0.0.0.0
   Default Gateway.................: ::
                                     0.0.0.0

Bluetooth Connection:

   Connection-specific DNS Suffix..: 
   Link-local IPv6 Address.........: ::
   IPv6 Address....................: ::
   IPv4 Address....................: 0.0.0.0
   Subnet Mask.....................: 0.0.0.0
   Default Gateway.................: ::
                                     0.0.0.0

```
Как мы видим, адрес IPv6 компьютером не получен.

b. Активируем IPv6-маршрутизацию на R1 с помощью команды IPv6 __unicast-routing__

```
R1>
R1>en
Password: 
Password: 
R1#
R1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#
R1(config)#ipv6 uni
R1(config)#ipv6 unicast-routing 
R1(config)#
```
Это позволит компьютерам получать IP-адреса и данные шлюза по умолчанию с помощью функции SLAAC. 
Проверим параметры группы маршрутизации введя команду __Show ipv6 interface__

```
R1#sh ipv6 int 
GigabitEthernet0/0 is up, line protocol is up
  IPv6 is enabled, link-local address is FE80::1
  No Virtual link-local address(es):
  Global unicast address(es):
    2001:DB8:ACAD:A::1, subnet is 2001:DB8:ACAD:A::/64
  Joined group address(es):
    FF02::1
    FF02::2
    FF02::1:FF00:1
  MTU is 1500 bytes
  ICMP error messages limited to one every 100 milliseconds
  ICMP redirects are enabled
  ICMP unreachables are sent
  ND DAD is enabled, number of DAD attempts: 1
  ND reachable time is 30000 milliseconds
  ND advertised reachable time is 0 (unspecified)
  ND advertised retransmit interval is 0 (unspecified)
  ND router advertisements are sent every 200 seconds
  ND router advertisements live for 1800 seconds
  ND advertised default router preference is Medium
  Hosts use stateless autoconfig for addresses.
GigabitEthernet0/1 is up, line protocol is up
  IPv6 is enabled, link-local address is FE80::1
  No Virtual link-local address(es):
  Global unicast address(es):
    2001:DB8:ACAD:1::1, subnet is 2001:DB8:ACAD:1::/64
  Joined group address(es):
    FF02::1
    FF02::2
    FF02::1:FF00:1
  MTU is 1500 bytes

 ```
Теперь оба интерфейса g0/0  и g0/1 входят в группу маршрутизации __FF02::2__


c. Теперь, когда R1 входит в группу многоадресной рассылки всех маршрутизаторов, еще раз введем команду __ipconfig__ на PC-B и проверим данные IPv6-адреса:

```
C:\>ipconfig

FastEthernet0 Connection:(default port)

   Connection-specific DNS Suffix..: 
   Link-local IPv6 Address.........: FE80::2E0:F9FF:FE4D:298C
   IPv6 Address....................: 2001:DB8:ACAD:1:2E0:F9FF:FE4D:298C
   IPv4 Address....................: 0.0.0.0
   Subnet Mask.....................: 0.0.0.0
   Default Gateway.................: FE80::1
                                     0.0.0.0
```

ПК PC-B подключен к группе маршрутизации. Используя протокол EUI64 сгенерировал себе префикс маршрутизации и идентификатор подсети, используя свой MAC адрес.

#### **Шаг 3. Назначаем IPv6-адрес интерфейсу управления (SVI) на S1.**

a. Назначим адрес IPv6 и локальный адрес канала для коммутатора S1. 

__Примечание.__ Шаблон по умолчанию менеджера базы данных 2960 Switch Database Manager (SDM) не поддерживает IPv6. Перед назначением IPv6-адреса SVI VLAN 1 может понадобиться выполнение команды __sdm prefer dual-ipv4-and-ipv6 default__ для включения IPv6-адресации.



```
S1#
S1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
S1(config)#
S1(config)#sdm prefer dual-ipv4-and-ipv6 default
Changes to the running SDM preferences have been stored, but cannot take effect until the next reload.
Use 'show sdm prefer' to see what SDM preference is currently active.
S1(config)#
```

__Примечание.__ Шаблон __default bias__, который по умолчанию используется диспетчером SDM (диспетчер базы данных коммутатора), не предоставляет возможностей адресации IPv6. Убедимся, что SDM использует шаблон __dual-ipv4-and-ipv6__ или __lanbase-routing__. Новый шаблон будет использоваться после перезагрузки. 

```
S1#
S1#show sdm prefer
 The current template is "dual-ipv4-and-ipv6 default" template.
```

Назначаем адрес IPv6:

```
S1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
S1(config)#
S1(config)#int vlan1
S1(config)#int vlan1
S1(config-if)#ipv6 address 2001:db8:acad:1::b/64
S1(config-if)#
S1(config-if)#no sh
S1(config-if)#no shutdown 

S1(config-if)#
%LINK-5-CHANGED: Interface Vlan1, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface Vlan1, changed state to up
S1(config-if)#
```
Назначаем интерфейсу локальный адрес канала:

```
S1(config-if)#
S1(config-if)#ipv6 address fe80::1 lin
S1(config-if)#ipv6 address fe80::1 link-local 
S1(config-if)#
S1(config-if)#no sh
S1(config-if)#no shutdown 
S1(config-if)#end
S1#
```

b. Проверяем правильность назначения IPv6-адресов интерфейсу управления с помощью команды __show ipv6 interface vlan1.__

```
S1#show ipv6 interface vlan 1
Vlan1 is up, line protocol is up
  IPv6 is enabled, link-local address is FE80::1
  No Virtual link-local address(es):
  Global unicast address(es):
    2001:DB8:ACAD:1::B, subnet is 2001:DB8:ACAD:1::/64
  Joined group address(es):
    FF02::1
    FF02::1:FF00:1
    FF02::1:FF00:B
  MTU is 1500 bytes
  ICMP error messages limited to one every 100 milliseconds
  ICMP redirects are enabled
  ICMP unreachables are sent
  Output features: Check hwidb
  ND DAD is enabled, number of DAD attempts: 1
  ND reachable time is 30000 milliseconds
S1#
```

#### **Шаг 4. Назначаем компьютерам статические IPv6-адреса.**

a. Откроем окно Свойства Ethernet и назначим адресацию IPv6 для ПК PC-A:

[![PC-A.png](https://i.postimg.cc/28vjk6cs/PC-A.png)](https://postimg.cc/YhrB8pwR)

[![PC-B.png](https://i.postimg.cc/C1ND67D4/PC-B.png)](https://postimg.cc/hzXfX92J)

Откроем окно Свойства Ethernet и назначим адресацию IPv6 для ПК PC-B:

[![2024-04-02-16-00-26.png](https://i.postimg.cc/k5hZkNmW/2024-04-02-16-00-26.png)](https://postimg.cc/06DcSwrN)

[![2024-04-02-16-00-42.png](https://i.postimg.cc/PJZ5Lr4F/2024-04-02-16-00-42.png)](https://postimg.cc/JHrLv8Sb)

b.	Проверим, что оба компьютера имеют правильную информацию адреса IPv6. Каждый компьютер должен иметь два глобальных адреса IPv6: один статический и один SLACC

PC-A в статике

```
C:\>ipconfig

FastEthernet0 Connection:(default port)

   Connection-specific DNS Suffix..: 
   Link-local IPv6 Address.........: FE80::2D0:FFFF:FE01:B1BB
   IPv6 Address....................: 2001:DB8:ACAD:1::3
   IPv4 Address....................: 0.0.0.0
   Subnet Mask.....................: 0.0.0.0
   Default Gateway.................: FE80::1
                                     0.0.0.0

```

PC-A в динамике:

```
C:\>ipconfig

FastEthernet0 Connection:(default port)

   Connection-specific DNS Suffix..: 
   Link-local IPv6 Address.........: FE80::2D0:FFFF:FE01:B1BB
   IPv6 Address....................: 2001:DB8:ACAD:A:2D0:FFFF:FE01:B1BB
   IPv4 Address....................: 0.0.0.0
   Subnet Mask.....................: 0.0.0.0
   Default Gateway.................: FE80::1
                                     0.0.0.0

Bluetooth Connection:
```
PC-B в статике:

```
C:\>ipconfig

FastEthernet0 Connection:(default port)

   Connection-specific DNS Suffix..: 
   Link-local IPv6 Address.........: FE80::2E0:F9FF:FE4D:298C
   IPv6 Address....................: 2001:DB8:ACAD:A::3
   IPv4 Address....................: 0.0.0.0
   Subnet Mask.....................: 0.0.0.0
   Default Gateway.................: FE80::1
                                     0.0.0.0

Bluetooth Connection:
```
PC-B в динамике:

```
C:\>ipconfig

FastEthernet0 Connection:(default port)

   Connection-specific DNS Suffix..: 
   Link-local IPv6 Address.........: FE80::2E0:F9FF:FE4D:298C
   IPv6 Address....................: 2001:DB8:ACAD:1:2E0:F9FF:FE4D:298C
   IPv4 Address....................: 0.0.0.0
   Subnet Mask.....................: 0.0.0.0
   Default Gateway.................: FE80::1
                                     0.0.0.0

Bluetooth Connection:
```

### **Часть 3. Проверка сквозного соединения**

- Отправим Эхо запрос на __FE80::1__ с компьютера PC-A Это локальный адрес канала, назначенный g0/1 на маршрутизаторе R1:

```
C:\>ping fe80::1

Pinging fe80::1 with 32 bytes of data:

Reply from FE80::1: bytes=32 time<1ms TTL=255
Reply from FE80::1: bytes=32 time<1ms TTL=255
Reply from FE80::1: bytes=32 time<1ms TTL=255
Reply from FE80::1: bytes=32 time<1ms TTL=255

Ping statistics for FE80::1:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms

C:\>
```
- Отправим запрос на интерфейс управления коммутатора S1 

```
C:\>ping 2001:db8:acad:1::b

Pinging 2001:db8:acad:1::b with 32 bytes of data:

Reply from 2001:DB8:ACAD:1::B: bytes=32 time<1ms TTL=255
Reply from 2001:DB8:ACAD:1::B: bytes=32 time<1ms TTL=255
Reply from 2001:DB8:ACAD:1::B: bytes=32 time<1ms TTL=255
Reply from 2001:DB8:ACAD:1::B: bytes=32 time<1ms TTL=255

Ping statistics for 2001:DB8:ACAD:1::B:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms

C:\>
```
- Введем команду __tracert__ на PC-A, чтобы проверить наличие сквозного подключения к PC-B.

```
C:\>tracert
Cisco Packet Tracer PC Tracert

Usage: tracert target

C:\>
```
- С PC-B отправим эхо-запрос на PC-A.

```
:\>
C:\>ping 2001:DB8:ACAD:A::2E

Pinging 2001:DB8:ACAD:A::2E with 32 bytes of data:

Reply from 2001:DB8:ACAD:A::2E: bytes=32 time<1ms TTL=127
Reply from 2001:DB8:ACAD:A::2E: bytes=32 time<1ms TTL=127
Reply from 2001:DB8:ACAD:A::2E: bytes=32 time<1ms TTL=127
Reply from 2001:DB8:ACAD:A::2E: bytes=32 time<1ms TTL=127

Ping statistics for 2001:DB8:ACAD:A::2E:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms

C:\>
```
- С PC-B отправbv эхо-запрос на локальный адрес канала G0/0 на R1 (2001:DB8:ACAD:A::1)

```
C:\>ping 2001:DB8:ACAD:A::1

Pinging 2001:DB8:ACAD:A::1 with 32 bytes of data:

Reply from 2001:DB8:ACAD:A::1: bytes=32 time<1ms TTL=255
Reply from 2001:DB8:ACAD:A::1: bytes=32 time<1ms TTL=255
Reply from 2001:DB8:ACAD:A::1: bytes=32 time<1ms TTL=255
Reply from 2001:DB8:ACAD:A::1: bytes=32 time<1ms TTL=255

Ping statistics for 2001:DB8:ACAD:A::1:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms

C:\>
```

Так как локальные адреса интерфейсов подключают физически разные сети, то для каждого интерфейса можно настроить 1 и тот же локальный адрес сети.

Настройка завершена.
