#  Базовая настройка коммутатора

------------



###  Задание:
###### **Часть 1. Проверка конфигурации коммутатора по умолчанию** 
###### **Часть 2. Создание сети и настройка основных параметров устройства**



- Разработать и задокументировать адресное пространство
- Настроить IP адреса на каждом активном порту

###### **Часть 3. Проверка сетевых подключений**

  - Отобразите конфигурацию устройства
  - Протестируйте сквозное соединение, отправив эхо-запрос.
  - Протестируйте возможности удаленного управления с помощью Telnet.
  
###  	Общие сведения/сценарий



 На коммутаторах Cisco можно настроить особый IP-адрес, который называют виртуальным интерфейсом коммутатора (SVI). SVI или адрес управления можно использовать для удаленного доступа к коммутатору в целях отображения или настройки параметров. Если для SVI сети VLAN 1 назначен IP-адрес, то по умолчанию все порты в сети VLAN 1 имеют доступ к IP-адресу управления SVI.
 В ходе данной лабораторной работы вам предстоит построить простую топологию, используя Ethernet-кабель локальной сети, и получить доступ к коммутатору Cisco, используя консольное подключение и методы удаленного доступа. Перед настройкой базовых параметров коммутатора нужно проверить настройки коммутатора по умолчанию. В число таких основных параметров коммутации входят имя устройства, описание интерфейса, локальные пароли, объявление дня (MOTD), IP-адрес и статический MAC-адрес. Необходимо также показать использование IP-адреса управления для удаленного управления коммутатором. Топология включает один коммутатор и один узел с использованием только портов Ethernet и консольных портов.
 
 __Примечание__. В лабораторной работе используются коммутаторы Cisco Catalyst 2960s с операционной системой Cisco IOS 15.2(2) (образ lanbasek9). Допускается использование других моделей коммутаторов и других версий Cisco IOS. В зависимости от модели устройства и версии Cisco IOS доступные команды и результаты их выполнения могут отличаться от тех, которые показаны в лабораторных работах.
 
 __Примечание__. Убедитесь, что все настройки коммутатора удалены и загрузочная конфигурация отсутствует. Если вы не уверены, обратитесь к инструктору. Процедура инициализации и перезагрузки коммутатора описана в приложении А
 
 ### Часть 1. Создание сети и проверка настроек коммутатора по умолчанию
   В первой части работы нам нужно настроить топологию сети и проверить настройку коммутатора по умолчанию.
   
   #### Топологическая схема. Рисунок 1.
  
   ![image](https://github.com/KVV2015/-1/assets/162564909/fac5f4e8-f5a4-4acb-8140-b16822db5a0e)
   

   #### Шаг1.Создаем сеть согласно топологии.
- Подсоединяем консольный кабелькак показано в топологии (рисунок1). На данном этапе не подключаем кабель Ethernet компьютера PC-A.
- Устанавливаем консольное подключение к коммутатору с компьютера PC-A с помощью Tera Term или другой программы эмуляции терминала

   #### Шаг2.Проверяем настройки коммутатора по умолчанию.

На данном этапе нам нужно проверить такие параметры коммутатора по умолчанию, как текущие настройки коммутатора, данные IOS, свойства интерфейса, сведения о VLAN и флеш-память.

Все команды IOS коммутатора можно выполнять из привилегированного режима. Доступ к привилегированному режиму нужно ограничить с помощью пароля, чтобы предотвратить неавторизованное использование устройства — через этот режим можно получить прямой доступ к режиму глобальной конфигурации и командам, используемым для настройки рабочих параметров. Пароли можно будет настроить чуть позже.

- Вводим команду **enable**, чтобы войти в привилегированный режим:

  ```$ < Switch>
  Switch>
  Switch>en
  Switch>enable 
  Switch#
  Switch#
  
  ```

- Убедимся, что на коммутаторе находится пустой файл конфигурации по умолчанию, с помощью команды **show running-config** привилегированного режима:

  ``` Switch#show run
  Switch#show run
  Switch#show running-config 
  Building configuration...
  Current configuration : 1080 bytes
  !
  version 15.0
  no service timestamps log datetime msec
  no service timestamps debug datetime msec
  no service password-encryption
  !
  hostname Switch
  !
  !
  !
  !
  !
  !
  spanning-tree mode pvst
  spanning-tree extend system-id
  !
  interface FastEthernet0/1
  !
  interface FastEthernet0/2
  !
  interface FastEthernet0/3
  !
  interface FastEthernet0/4
  !
  interface FastEthernet0/5
  !
  interface FastEthernet0/6
  !
  interface FastEthernet0/7
  !
  interface FastEthernet0/8
  !
  interface FastEthernet0/9
  !
  interface FastEthernet0/10
  !
  interface FastEthernet0/11
  !
  interface FastEthernet0/12
  !
  interface FastEthernet0/13
  !
  interface FastEthernet0/14
  !
  interface FastEthernet0/15
  !
  interface FastEthernet0/16
  !
  interface FastEthernet0/17
  !
  interface FastEthernet0/18
  !
  interface FastEthernet0/19
  !
  interface FastEthernet0/20
  !
  interface FastEthernet0/21
  !
  interface FastEthernet0/22
  !
  interface FastEthernet0/23
  !
  interface FastEthernet0/24
  !
  interface GigabitEthernet0/1
  !
  interface GigabitEthernet0/2
  !
  interface Vlan1
  no ip address
  shutdown
  !
  !
  !
  !
  line con 0
  !
  line vty 0 4
  login
  line vty 5 15
  login
  !
  !
  !
  !
  end
  ```

  Файл конфигурации не имеет ранее записанных настроек, коммутатор не имеет настроенного IP адреса и паролей. Файл конфигурации сообщает:

  - Количество интерфейсов FastEthernet равно 24 
  - Количество интерфейсов Gigabit Ethernet равно 2 
  - Диапазон значений в vty-линиях (0-4) и (5-15) 

  

- Изучим файл загрузочной конфигурации, который содержится в энергонезависимом ОЗУ (NVRAM). Для этого из привилегированного режима введем команду (startup configuration):

  ```Switch#
  Switch#
  Switch#
  Switch#show star
  Switch#show startup-config 
  startup-config is not present
  ```

  Файл еще не создан, о чем говорит сообщение **startup-config startup-config is not present **

- Изучим характеристики SVI для VLAN 1. Для этого в привелегированном режиме введем команду show interfaces vlan1:

  ``` Switch#show interfaces vlan1
  Switch#show interfaces vlan1
  Vlan1 is administratively down, line protocol is down
    Hardware is CPU Interface, address is 0010.1140.0055 (bia 0010.1140.0055)
    MTU 1500 bytes, BW 100000 Kbit, DLY 1000000 usec,
       reliability 255/255, txload 1/255, rxload 1/255
    Encapsulation ARPA, loopback not set
    ARP type: ARPA, ARP Timeout 04:00:00
    Last input 21:40:21, output never, output hang never
    Last clearing of "show interface" counters never
    Input queue: 0/75/0/0 (size/max/drops/flushes); Total output drops: 0
    Queueing strategy: fifo
    Output queue: 0/40 (size/max)
    5 minute input rate 0 bits/sec, 0 packets/sec
    5 minute output rate 0 bits/sec, 0 packets/sec
       1682 packets input, 530955 bytes, 0 no buffer
       Received 0 broadcasts (0 IP multicast)
       0 runts, 0 giants, 0 throttles
       0 input errors, 0 CRC, 0 frame, 0 overrun, 0 ignored
       563859 packets output, 0 bytes, 0 underruns
       0 output errors, 23 interface resets
       0 output buffer failures, 0 output buffers swapped out
  ```

  Данный файл сообщает, что порт выключен, его **MAC адрес bia 0010.1140.0055**. Чтобы узнать его IP адрес введем команду **show running-config**

    Switch#show run
    Switch#show running-config 
    Building configuration...
    Current configuration : 1080 bytes
    !
    version 15.0
    !
    !
    interface Vlan1
    no ip address
    shutdown
    

  Из данного сообщения мы видим, что IP адрес не назначен, а чтобы более подробно посмотреть свойста IP адреса, введем команду **show ip interface vlan1**

``` witch#show ip interface vlan1
Switch#show ip interface vlan1
Vlan1 is administratively down, line protocol is down
  Internet protocol processing disabled

Switch#
```

- Подсоединяем кабель Ethernet компьютера PC-A к порту 6 на коммутаторе и изучим IP-свойства интерфейса SVI сети VLAN 1 введя команду **show interfaces vlan1**

  ``` 
  Switch#
  %LINK-5-CHANGED: Interface FastEthernet0/6, changed state to up
  
  %LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/6, changed state to up
  ```

  ```
  Switch#show interfaces vlan1
  Vlan1 is administratively down, line protocol is down
    Hardware is CPU Interface, address is 0010.1140.0055 (bia 0010.1140.0055)
    MTU 1500 bytes, BW 100000 Kbit, DLY 1000000 usec,
       reliability 255/255, txload 1/255, rxload 1/255
  ```

  

-    Изучим сведения о версии ОС Cisco IOS на коммутаторе, введя команду **show running-config** 

 

``` 
Switch#show run
Building configuration...

Current configuration : 1080 bytes
!
version 15.0
no service timestamps log datetime msec
no service timestamps debug datetime msec
no service password-encryption
!
hostname Switch
!
```

Здесь мы видим, что версия ОС Cisco IOS на коммутаторе 15.0, пароль не задан. Чтобы посмотреть состояние файла образа системы и состояние флеш памяти введем команду **show flash**

``` 
Switch#show flash: 
Directory of flash:/

1 -rw- 4670455 <no date> 2960-lanbasek9-mz.150-2.SE4.bin

64016384 bytes total (59345929 bytes free)
Switch#
```

Здесь мы видем что в памяти находится лишь файл образа системы 2960-lanbasek9-mz.150-2.SE4.bin

- Изучим свойства по умолчанию интерфейса FastEthernet, который используется компьютером PC-A, введя команду **show interface f0/6** 

```
Switch#show interfaces fa0/6
FastEthernet0/6 is up, line protocol is up (connected)
  Hardware is Lance, address is 0001.9763.0806 (bia 0001.9763.0806)
 BW 100000 Kbit, DLY 1000 usec,
     reliability 255/255, txload 1/255, rxload 1/255
  Encapsulation ARPA, loopback not set
  Keepalive set (10 sec)
  Full-duplex, 100Mb/s
  input flow-control is off, output flow-control is off
  ARP type: ARPA, ARP Timeout 04:00:00
  Last input 00:00:08, output 00:00:05, output hang never
  Last clearing of "show interface" counters never
  Input queue: 0/75/0/0 (size/max/drops/flushes); Total output drops: 0
  Queueing strategy: fifo
  Output queue :0/40 (size/max)
  5 minute input rate 0 bits/sec, 0 packets/sec
  5 minute output rate 0 bits/sec, 0 packets/sec
     956 packets input, 193351 bytes, 0 no buffer
     Received 956 broadcasts, 0 runts, 0 giants, 0 throttles
     0 input errors, 0 CRC, 0 frame, 0 overrun, 0 ignored, 0 abort
     0 watchdog, 0 multicast, 0 pause input
     0 input packets with dribble condition detected
     2357 packets output, 263570 bytes, 0 underruns
     0 output errors, 0 collisions, 10 interface resets
     0 babbles, 0 late collision, 0 deferred
     0 lost carrier, 0 no carrier
     0 output buffer failures, 0 output buffers swapped out

Switch#
```

Мы видим, что интерфейс включен, его скорость составляет 100 Мб/с, MAC адрес bia 0001.9763.0806. Настройки дуплекса DLY 1000 usec.


## Часть 2. Настройка базовых параметров сетевых устройств

Во второй части настроим основные параметры коммутатора и компьютера.

### Шаг 1. Настраиваем базовые параметры коммутатора

- В режиме глобальной конфигурации скопируем следующие базовые параметры конфигурации и вставьте их в файл на коммутаторе S1:

   - no ip domain-lookup

   - hostname S1

   - service password-encryption

   - enable secret class

   - banner motd @ Unauthorized access is strictly prohibited. @

```
Switch(config)#no ip domain-lookup
Switch(config)#
Switch(config)#
Switch(config)#hostname S1
S1(config)#
S1(config)#
S1(config)#service password-encryption
S1(config)#
S1(config)#enable secret class
S1(config)#
S1(config)#banner motd # Unauthorized access is strictly prohibited. #
S1(config)#
```

- Назначим IP-адрес интерфейсу SVI на коммутаторе. Благодаря этому получим возможность удаленного управления коммутатором:

  Настройка IP адреса коммутатора ведется из глабальной конфигурации коммутатора. Устанавливаем IP адрес и маску подсети, а так же включаем порт.
 Подключаемся к комутатору с консоли и вводим следующие комманды:

  ``` 
  S1(config)#interface vlan1
  S1(config-if)#
  S1(config-if)#ip addres 192.168.1.1 255.255.255.0
  S1(config-if)#
  S1(config-if)#no shut
  S1(config-if)#no shutdown 
  
  S1(config-if)#
  %LINK-5-CHANGED: Interface Vlan1, changed state to up
  
  %LINEPROTO-5-UPDOWN: Line protocol on Interface Vlan1, changed state to up
  
  S1(config-if)#
  ```

- Доступ через порт консоли ограничим с помощью пароля cisco, для этого введем команды **Line console 0** , **password** и **Login** из привелегированного режима:

  ``` 
  S1(config)#line console 0
  S1(config-line)#
  S1(config-line)#pass
  S1(config-line)#password cisco
  S1(config-line)#
  S1(config-line)#login
  S1(config-line)#
  ```

​    Чтобы консольные сообщения не прерывали выполнение команд, используйте параметр **logging    synchronous** из режима глобальных настроек

``` 
S1(config)# line con 0
S1(config-line)# logging synchronous 
```

- Настроим каналы виртуального соединения для удаленного управления (vty), чтобы коммутатор разрешил доступ через Telnet. Для этого зададим зададим пароль для канала VTY, чтобы подключится к коммутатору по протоколу Telnet 

  Для этого из режима глобальных настроек войдем в порт 0-4, затем настроим пароль class и разрешим портам подключение к ним по протоколу Telnet

  ``` 
  S1(config)#line vty 0 4
  S1(config-line)#
  S1(config-line)#pass
  S1(config-line)#password class
  S1(config-line)#
  S1(config-line)#login
  S1(config-line)#
  S1(config-line)#trans
  S1(config-line)#transport inp
  S1(config-line)#transport input tel
  S1(config-line)#transport input telnet 
  S1(config-line)#
  ```

  Команда login необходима для включения пароля.

  

### Шаг 2. Настройка IP-адреса компьютера PC-A.

Назначаем компьютеру IP адрес и маску подсети в соответствии с таблицей адресации

1. Перейдите в меню **IP configuration**
2. Введите в поле строки **IPv4 Address** числовое значение адреса 192.168.1.2
3. Введите в поле строки **Subnet mask** значения маски подсити 255.255.255.0
4. Закройте меню **IP configuration**

## Часть 3. Проверка сетевых подключений

В третьей части нам предстоит проверить и задокументировать конфигурацию коммутатора, протестировать сквозное соединение между компьютером PC-A и коммутатором S1, а также протестировать возможность удаленного управления коммутатором. 

### Шаг 1. Отображение конфигурации коммутатора.

Проверим конфигурацию нашего коммутатора введя команду **show run** из привелигированного режима:

``` 
S1#
S1#show run
Building configuration...

Current configuration : 1317 bytes
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
!
interface FastEthernet0/2
!
interface FastEthernet0/3
!
interface FastEthernet0/4
!
interface FastEthernet0/5
!
interface FastEthernet0/6
!
interface FastEthernet0/7
!
interface FastEthernet0/8
!
interface FastEthernet0/9
!
interface FastEthernet0/10
!
interface FastEthernet0/11
!
interface FastEthernet0/12
!
interface FastEthernet0/13
!
interface FastEthernet0/14
!
interface FastEthernet0/15
!
interface FastEthernet0/16
!
interface FastEthernet0/17
!
interface FastEthernet0/18
!
interface FastEthernet0/19
!
interface FastEthernet0/20
!
interface FastEthernet0/21
!
interface FastEthernet0/22
!
interface FastEthernet0/23
!
interface FastEthernet0/24
!
interface GigabitEthernet0/1
!
interface GigabitEthernet0/2
!
interface Vlan1
 ip address 192.168.1.1 255.255.255.0
!
banner motd ^C Unauthorized access is strictly prohibited. ^C
!
!
!
line con 0
 password 7 0822455D0A16
 logging synchronous
 login
!
line vty 0 4
 password 7 0822404F1A0A
 login
 transport input telnet
line vty 5 15
 login
!
!
!
!
end


S1#
```



### Шаг 2. Проверка удаленного управления коммутатором S1.

Для этого используем удаленный доступ к устройству с помощью Telnet. 

- Открываем Tera term 
- Выбираем сервер Telnet и указываем адрес управления SVI для подключения к S1. 
- Вводим пароль
- Входим в привелигированный режим 
- Вводим пароль
- Сохраняем конфигурацию командой **Copy run start**
- Завершаем сеанс командой **Exit**

``` 
Trying 192.168.1.1 ...Open Unauthorized access is strictly prohibited. 


User Access Verification

Password: 
S1>
S1>
S1>en
S1>enable 
Password: 
S1#
S1#
S1#copy run start
Destination filename [startup-config]? 
Building configuration...
[OK]
S1#
S1#exit


Trying 192.168.1.1 ...Open Unauthorized access is strictly prohibited. 


User Access Verification

Password: 
```

На этом настройка завершена
