# Лабраторная работа - Настройка DHCPv6

### Топология сети

[![2024-05-23-14-16-16.png](https://i.postimg.cc/c4mRsLT3/2024-05-23-14-16-16.png)](https://postimg.cc/KR1kfF6c)

### Таблица адресации

| Устройство | Интерфейс | IPv6-адрес            |
|------------|-----------|-----------------------|
| R1         | G0/0      | 2001:db8:acad:2::1/64 |
|            |           | fe80::1               |
|            | G0/1      | 2001:db8:acad:1::1/64 |
|            |           | fe80::1               |
| R2         | G0/0      | 2001:db8:acad:2::2/64 |
|            |           | fe80::2               |
|            | G0/1      | 2001:db8:acad:3::1/64 |
|            |           | fe80::1               |
| PC-A       | NIC       | DHCP                  |
| PC-B       | NIC       | DHCP                  |

## Задачи

  __Часть 1. Создание сети и настройка основных параметров устройства__

  __Часть 2. Проверка назначения адреса SLAAC от R1__

  __Часть 3. Настройка и проверка сервера DHCPv6 без гражданства на R1__

  __Часть 4. Настройка и проверка состояния DHCPv6 сервера на R1__

  __Часть 5. Настройка и проверка DHCPv6 Relay на R2__
  

## Общие сведения

Динамическое назначение глобальных индивидуальных IPv6-адресов можно настроить тремя способами:

•	Автоматическая конфигурация адреса без сохранения состояния (Stateless Address Autoconfiguration, SLAAC)

•	DHCPv6 без отслеживания состояния

•	Адресация DHCPv6 с учетом состояний.

При использовании SLACC для назначения адресов IPv6 хостам сервер DHCPv6 не используется. Поскольку DHCPv6 сервер не используется при реализации SLACC, хосты не могут получать дополнительную важную сетевую информацию, включая адрес сервера доменных имен (DNS), а также имя домена.

При использовании Stateless DHCPv6 для назначения адресов IPv6 хосту сервер DHCPv6 используется для назначения дополнительной важной информации о сети, однако адрес IPv6 назначается с помощью SLACC.

При использовании DHCPv6 с отслеживанием состояния, сервер DHCP назначает всю информацию, включая IPv6-адрес узла.

Определение способа получения динамической IPv6-адресации зависит от установленных значений флагов, содержащихся в объявлениях маршрутизатора (сообщениях RA).

В предложенном сценарии размеры компании увеличились, и сетевые администраторы больше не имеют возможности назначать IP-адреса для устройств вручную. Ваша задача - настроить маршрутизатор R2 для назначения адресов IPv6 в двух разных подсетях, подключенных к маршрутизатору R1.

## Инструкции

 ## Часть 1. Создание сети и настройка основных параметров устройства

 В первой части лабораторной работы вам предстоит создать топологию сети и настроить базовые параметры для узлов ПК и коммутаторов

### Шаг 1. Создайте сеть согласно топологии.

### Шаг 2. Настройте базовые параметры каждого коммутатора. (необязательно)

a.	Присвойте коммутатору имя устройства.

b.	Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов

c.	Назначьте class в качестве зашифрованного пароля привилегированного режима EXEC.

d.	Назначьте cisco в качестве пароля консоли и включите вход в систему по паролю.

e.	Назначьте cisco в качестве пароля VTY и включите вход в систему по паролю.

f.	Зашифруйте открытые пароли.

g.	Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.

h.	Отключите все неиспользуемые порты.

i.	Сохраните текущую конфигурацию в файл загрузочной конфигурации.
Закройте окно настройки.

### Шаг 3. Произведите базовую настройку маршрутизаторов.

a.	Назначьте маршрутизатору имя устройства.

b.	Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.

c.	Назначьте class в качестве зашифрованного пароля привилегированного режима EXEC.

d.	Назначьте cisco в качестве пароля консоли и включите вход в систему по паролю.

e.	Назначьте cisco в качестве пароля VTY и включите вход в систему по паролю.

f.	Зашифруйте открытые пароли.

g.	Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.

h.	Активируйте IPv6-маршрутизацию

i.	Сохраните текущую конфигурацию в файл загрузочной конфигурации


```
Router(config)#
Router(config)#host R1
R1(config)#
R1(config)#
R1(config)#no ip domain-l
R1(config)#no ip domain-lookup 
R1(config)#
R1(config)#ipv6 unicast-
R1(config)#ipv6 unicast-routing 
R1(config)#
R1(config)#^Z
R1#
%SYS-5-CONFIG_I: Configured from console by console

R1#
R1#wr
Building configurat
```

### Шаг 4. Настройка интерфейсов и маршрутизации для обоих маршрутизаторов.

a. Настройте интерфейсы G0/0 и G0/1 на R1 и R2 с адресами IPv6, указанными в таблице выше:

```
R1(config)#
R1(config)#int g0/0
R1(config-if)#
R1(config-if)#
R1(config-if)#
R1(config-if)#ipv6 add
R1(config-if)#ipv6 address 2001:db8:acad:2::1/64
R1(config-if)#
R1(config-if)#ipv6 address fe80::1 link
R1(config-if)#ipv6 address fe80::1 link-local 
R1(config-if)#
R1(config-if)#no sh

R1(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet0/1, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/1, changed state to up

R1(config-if)#ex
R1(config)#
R1(config)#int g0/1
R1(config-if)#
R1(config-if)#ipv6 add
R1(config-if)#ipv6 address 2001:db8:acad:1::1/64
R1(config-if)#
R1(config-if)#ipv6 address fe80::1 link
R1(config-if)#ipv6 address fe80::1 link-local 
R1(config-if)#
R1(config-if)#no sh

R1(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet0/1, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/1, changed state to up

R1(config-if)#
R1(config-if)#^Z
R1#
%SYS-5-CONFIG_I: Configured from console by console

R1#
```

b. Настройте маршрут по умолчанию на каждом маршрутизаторе, который указывает на IP-адрес G0/0 на другом маршрутизаторе:

```
R1(config)#
R1(config)#ipv6 route 2001:db8:acad:3::/64 2001:db8:acad:2::2
R1(config)#
R2(config)#^Z
R1#wr
```
```
R2(config)#
R2(config)#ipv6 route 2001:db8:acad:1::/64 2001:db8:acad:2::1
R2(config)#
R2(config)#^Z
R2#wr
```


c. Убедитесь, что маршрутизация работает с помощью пинга адреса G0/1 R2 из R1

Для проверки связи, присвою динамический IPv6 адрес:

- PC-A: 2001:db8:acad:1::2

- PC-B: 2001:db8:acad:3::2

Отправим эхо запрос с R1 на PC-B:
```
R1#ping 2001:db8:acad:3::2

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 2001:db8:acad:3::2, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 0/0/1 ms

R1#
```

Отправим эхо запрос с R2 на PC-A:

```
R2#ping 2001:db8:acad:1::2

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 2001:db8:acad:1::2, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 0/0/0 ms
```
Отправим эхо запрос с PC-A на PC-B:
```
C:\>ping 2001:db8:acad:3::2

Pinging 2001:db8:acad:3::2 with 32 bytes of data:

Reply from 2001:DB8:ACAD:3::2: bytes=32 time<1ms TTL=126
Reply from 2001:DB8:ACAD:3::2: bytes=32 time<1ms TTL=126
Reply from 2001:DB8:ACAD:3::2: bytes=32 time<1ms TTL=126
Reply from 2001:DB8:ACAD:3::2: bytes=32 time<1ms TTL=126

Ping statistics for 2001:DB8:ACAD:3::2:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms

C:\>
```
Все успешно.

d. Сохраните текущую конфигурацию в файл загрузочной конфигурации:

```
R1#sh run
Building configuration...

Current configuration : 912 bytes
!
version 15.1
no service timestamps log datetime msec
no service timestamps debug datetime msec
no service password-encryption
!
hostname R1
!
!
ip cef
ipv6 unicast-routing
!
no ipv6 cef
!
license udi pid CISCO2911/K9 sn FTX1524GY52-
!
no ip domain-lookup
!
!
spanning-tree mode pvst
!
interface GigabitEthernet0/0
 no ip address
 duplex auto
 speed auto
 ipv6 address FE80::1 link-local
 ipv6 address 2001:DB8:ACAD:2::1/64
!
interface GigabitEthernet0/1
 no ip address
 duplex auto
 speed auto
 ipv6 address FE80::1 link-local
 ipv6 address 2001:DB8:ACAD:1::1/64
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
!
ip flow-export version 9
!
ipv6 route 2001:DB8:ACAD:3::/64 2001:DB8:ACAD:2::2
!
!
!
!
end


R1#
```
## Часть 2. Проверка назначения адреса SLAAC от R1

В части 2 вы убедитесь, что узел PC-A получает адрес IPv6 с помощью метода SLAAC.
Включите PC-A и убедитесь, что сетевой адаптер настроен для автоматической настройки IPv6.

Через несколько минут результаты команды __ipconfig__ должны показать, что PC-A присвоил себе адрес из сети 2001:db8:ACAD:1::/64.

```
C:\>ipconfig

FastEthernet0 Connection:(default port)

   Connection-specific DNS Suffix..: 
   Link-local IPv6 Address.........: FE80::2
   IPv6 Address....................: 2001:DB8:ACAD:1::2
   IPv4 Address....................: 0.0.0.0
   Subnet Mask.....................: 0.0.0.0
   Default Gateway.................: FE80::1
                                     0.0.0.0
```
## Часть 3. Настройка и проверка сервера DHCPv6 на R1

В части 3 выполняется настройка и проверка состояния DHCP-сервера на R1. Цель состоит в том, чтобы предоставить PC-A информацию о DNS-сервере и домене.

### Шаг 1. Более подробно изучите конфигурацию PC-A.

a.	Выполните команду ipconfig /all на PC-A и посмотрите на результат.

```
C:\>ipconfig /all

FastEthernet0 Connection:(default port)

   Connection-specific DNS Suffix..: 
   Physical Address................: 00E0.A3D1.964E
   Link-local IPv6 Address.........: FE80::2
   IPv6 Address....................: 2001:DB8:ACAD:1::2
   IPv4 Address....................: 0.0.0.0
   Subnet Mask.....................: 0.0.0.0
   Default Gateway.................: FE80::1
                                     0.0.0.0
   DHCP Servers....................: 0.0.0.0
   DHCPv6 IAID.....................: 
   DHCPv6 Client DUID..............: 00-01-00-01-04-85-9E-18-00-E0-A3-D1-96-4E
   DNS Servers.....................: ::
                                     0.0.0.0.
```
b.	Обратите внимание, что основной DNS-суффикс и сервер, отсутствует.


### Шаг 2. Настройте R1 для предоставления DHCPv6 без состояния для PC-A.

a.	Создайте пул DHCP IPv6 на R1 с именем R1-STATELESS. В составе этого пула назначьте адрес DNS-сервера, как 2001:db8:acad::1, а имя домена — как stateless.com.

```
R1#
R1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#
R1(config)#ipv6 dhcp pool R1-STATELESS
R1(config-dhcpv6)#
R1(config-dhcpv6)#dns-server 2001:db8:acad::254
R1(config-dhcpv6)#
R1(config-dhcpv6)#domain-name STATELESS.com
R1(config-dhcpv6)#
R1(config-dhcpv6)#ex
R1(config)#do wr
Building configuration...
[OK]
R1(config)#
```

b.	Настройте интерфейс G0/1 на R1, чтобы предоставить флаг конфигурации OTHER для локальной сети R1 и укажите только что созданный пул DHCP в качестве ресурса DHCP для этого интерфейса

```
R1(config)#
R1(config)#int g0/1
R1(config-if)#
R1(config-if)#
R1(config-if)#ipv6 nd other-config-flag 
R1(config-if)#
R1(config-if)#ipv6 dhcp server R1-STATELESS
R1(config-if)#
R1(config-if)#ex
R1(config)#
R1(config)#do wr
Building configuration...
[OK]
```

c.	Сохраните текущую конфигурацию в файл загрузочной конфигурации.

```
R1(config)#
R1(config)#do sh run
Building configuration...

Current configuration : 1058 bytes
!
version 15.1
no service timestamps log datetime msec
no service timestamps debug datetime msec
no service password-encryption
!
hostname R1
!

ip cef
ipv6 unicast-routing
!
no ipv6 cef
!
ipv6 dhcp pool R1-STATELESS
 dns-server 2001:DB8:ACAD::254
 domain-name STATELESS.com
!
license udi pid CISCO2911/K9 sn FTX1524GY52-
!
!
no ip domain-lookup
!
!
spanning-tree mode pvst
!
!
interface GigabitEthernet0/0
 no ip address
 duplex auto
 speed auto
 ipv6 address FE80::1 link-local
 ipv6 address 2001:DB8:ACAD:2::1/64
!
interface GigabitEthernet0/1
 no ip address
 duplex auto
 speed auto
 ipv6 address FE80::1 link-local
 ipv6 address 2001:DB8:ACAD:1::1/64
 ipv6 nd other-config-flag
 ipv6 dhcp server R1-STATELESS
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
!
ip flow-export version 9
!
ipv6 route 2001:DB8:ACAD:3::/64 2001:DB8:ACAD:2::2
!
!
no cdp run
!
!
end
```

d.	Перезапустите PC-A.

e.	Проверьте вывод __ipconfig /all__ и обратите внимание на изменения.

```
C:\>ipconfig /all

FastEthernet0 Connection:(default port)

   Connection-specific DNS Suffix..: STATELESS.com 
   Physical Address................: 00E0.A3D1.964E
   Link-local IPv6 Address.........: FE80::2
   IPv6 Address....................: 2001:DB8:ACAD:1::2
   IPv4 Address....................: 0.0.0.0
   Subnet Mask.....................: 0.0.0.0
   Default Gateway.................: FE80::1
                                     0.0.0.0
   DHCP Servers....................: 0.0.0.0
   DHCPv6 IAID.....................: 1569819070
   DHCPv6 Client DUID..............: 00-01-00-01-04-85-9E-18-00-E0-A3-D1-96-4E
   DNS Servers.....................: 2001:DB8:ACAD::254
                                     0.0.0.0
```

f. Тестирование подключения с помощью пинга IP-адреса интерфейса G0/1 R2

```
C:\>
C:\>ping 2001:db8:acad:3::1

Pinging 2001:db8:acad:3::1 with 32 bytes of data:

Reply from 2001:DB8:ACAD:3::1: bytes=32 time<1ms TTL=254
Reply from 2001:DB8:ACAD:3::1: bytes=32 time<1ms TTL=254
Reply from 2001:DB8:ACAD:3::1: bytes=32 time<1ms TTL=254
Reply from 2001:DB8:ACAD:3::1: bytes=32 time<1ms TTL=254

Ping statistics for 2001:DB8:ACAD:3::1:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms

C:\>
```

## Часть 4. Настройка сервера DHCPv6 с сохранением состояния на R1

В части 4 настраивается R1 для ответа на запросы DHCPv6 от локальной сети на R2

a.	Создайте пул DHCPv6 на R1 для сети 2001:db8:acad:3:aaa::/80. Это предоставит адреса локальной сети, подключенной к интерфейсу G0/1 на R2. В составе пула задайте DNS-сервер 2001:db8:acad: :254 и задайте доменное имя STATEFUL.com.

```
R2#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R2(config)#
R2(config)#ipv6 dhcp pool R2-STATEFUL
R2(config-dhcpv6)#
R2(config-dhcpv6)#address prefix 2001:db8:acad:3:aaa::/80
R2(config-dhcpv6)#
R2(config-dhcpv6)#dns-server 2001:db8:acad::254
R2(config-dhcpv6)#
R2(config-dhcpv6)#domain-name STATEFUL.com
R2(config-dhcpv6)#
R2(config-dhcpv6)#ex
R2(config)#do wr
Building configuration...
[OK]
R2(config)#
```
b.	Назначьте только что созданный пул DHCPv6 интерфейсу g0/0 на R1

```
R1(config)#int g0/0
R1(config-if)#
R1(config-if)#
R1(config-if)#ipv6 dhcp server R2-STATEFUL
R1(config-if)#
R1(config-if)#ex
R1(config)#
R1(config)#
R1(config)#do wr
Building configuration...
[OK]
```
## Часть 5. Настройка и проверка ретрансляции DHCPv6 на R2.

В части 5 необходимо настроить и проверить ретрансляцию DHCPv6 на R2, позволяя PC-B получать адрес IPv6.

### Шаг 1. Включите PC-B и проверьте адрес SLAAC, который он генерирует.

```
C:\>
ipconfig /all

FastEthernet0 Connection:(default port)

   Connection-specific DNS Suffix..: 
   Physical Address................: 0002.4A6A.EEB6
   Link-local IPv6 Address.........: FE80::2
   IPv6 Address....................: 2001:DB8:ACAD:3::2
   IPv4 Address....................: 0.0.0.0
   Subnet Mask.....................: 0.0.0.0
   Default Gateway.................: FE80::1
                                     0.0.0.0
   DHCP Servers....................: 0.0.0.0
   DHCPv6 IAID.....................: 
   DHCPv6 Client DUID..............: 00-01-00-01-36-CD-10-E2-00-02-4A-6A-EE-B6
   DNS Servers.....................: ::
                                     0.0.0.0

```

Обратите внимание на вывод, что используется префикс 2001:db8:acad:3::2

### Шаг 2. Настройте R2 в качестве агента DHCP-ретрансляции для локальной сети на G0/1.

__Примечание__: Данная функция не реализованна в оболочке Cisco Paket Tracer. Для ее реализации требуется:
 - Реальное оборудование 
 - Программа EVE-NG

a.	Настройте команду __ipv6 dhcp relay__ на интерфейсе __R2 G0/1__, указав адрес назначения интерфейса __G0/0 на R1__. Также настройте команду __managed-config-flag__

```
R2(config)#
R2(config)#int g0/1
R2(config-if)#
R2(config-if)#ipv6 nd managed-config-flag
R2(config-if)#
R2(config-if)#ipv6 dhcp relay 2001:db8:acad:2::1 g0/0
```
b.	Сохраните конфигурацию.

```
R2#copy running-config startup-config
Destination filename [startup-config]? 
Building configuration...
[OK]
R2#
```

### Шаг 3. Попытка получить адрес IPv6 из DHCPv6 на PC-B

a.	Перезапустите __PC-B__.

b.	Откройте командную строку на PC-B и выполните команду __ipconfig /all__ и проверьте выходные данные, чтобы увидеть результаты операции ретрансляции DHCPv6

c.	Проверьте подключение с помощью пинга IP-адреса интерфейса R1 G0/1.

