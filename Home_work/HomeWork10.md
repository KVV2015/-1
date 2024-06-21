# Лабораторная работа. Настройка протокола OSPFv2 для одной области

## Топология

[![2024-06-20-22-48-43.png](https://i.postimg.cc/Jnt8p8b6/2024-06-20-22-48-43.png)](https://postimg.cc/N9hZ0WCm)

## Таблица адресации

| Устройство | Интерфейс | IP-адрес    | Маска подсети |
|:----------:|:---------:|-------------|---------------|
|     R1     |   G0/0/1  | 10.53.0.1   | 255.255.255.0 |
| R1         | Loopback1 | 172.16.1.1  | 255.255.255.0 |
| R2         |   G0/0/1  | 10.53.0.2   | 255.255.255.0 |
| R2         | Loopback1 | 192.168.1.1 | 255.255.255.0 |
|            |           |             |               |
|            |           |             |               |

## Цели

Часть 1. Создание сети и настройка основных параметров устройства

Часть 2. Настройка и проверка базовой работы протокола  OSPFv2 для одной области

Часть 3. Оптимизация и проверка конфигурации OSPFv2 для одной области

## Общие сведения и сценарий

Вам было поручено настроить сеть небольшой компании с помощью OSPFv2. R1 будет размещать интернет-соединение (имитируемое интерфейсом Loopback 1) и делиться информацией о маршруте по умолчанию до  R2. После первоначальной настройки организация попросила оптимизировать конфигурацию, чтобы уменьшить трафик протокола и гарантировать, что R1 продолжает контролировать маршрутизацию.

__Примечание__. Статическая маршрутизация, используемая в данной лаборатории, заключается в оценке возможности настройки и настройки OSPFv2 в конфигурации для одной области. Этот подход, используемый в данной лаборатории, может не отражать рекомендации по работе с сетевыми сетями. 

__Примечание:__ Маршрутизаторы, используемые в практических лабораторных работах CCNA, - это Cisco 4221 с Cisco IOS XE Release 16.9.4 (образ universalk9). В лабораторных работах используются коммутаторы Cisco Catalyst 2960 с Cisco IOS версии 15.2(2) (образ lanbasek9). Можно использовать другие маршрутизаторы, коммутаторы и версии Cisco IOS. В зависимости от модели устройства и версии Cisco IOS доступные команды и результаты их выполнения могут отличаться от тех, которые показаны в лабораторных работах. Правильные идентификаторы интерфейса см. в сводной таблице по интерфейсам маршрутизаторов в конце лабораторной работы.

## Инструкции

### Часть 1. Создание сети и настройка основных параметров устройства


#### Шаг 1. Создайте сеть согласно топологии.

Подключите устройства, как показано в топологии, и подсоедините необходимые кабели.

#### Шаг 2. Произведите базовую настройку маршрутизаторов.

a.	Назначьте маршрутизатору имя устройства.

b.	Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.

c.	Назначьте class в качестве зашифрованного пароля привилегированного режима EXEC.

d.	Назначьте cisco в качестве пароля консоли и включите вход в систему по паролю.

e.	Назначьте cisco в качестве пароля VTY и включите вход в систему по паролю.

f.	Зашифруйте открытые пароли.

g.	Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.

h.	Сохраните текущую конфигурацию в файл загрузочной конфигурации.

#### Шаг 3. Настройте базовые параметры каждого коммутатора.

a.	Назначьте коммутатору имя устройства.
b.	Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.
c.	Назначьте class в качестве зашифрованного пароля привилегированного режима EXEC.
d.	Назначьте cisco в качестве пароля консоли и включите вход в систему по паролю.
e.	Назначьте cisco в качестве пароля VTY и включите вход в систему по паролю.
f.	Зашифруйте открытые пароли.
g.	Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.
h.	Сохраните текущую конфигурацию в файл загрузочной конфигурации.

### Часть 2. Настройка и проверка базовой работы протокола OSPFv2 для одной области

#### Шаг 1. Настройте адреса интерфейса и базового OSPFv2 на каждом маршрутизаторе.

a.	Настройте адреса интерфейсов на каждом маршрутизаторе, как показано в таблице адресации выше.
```
R1(config)#
R1(config)#int g0/1
R1(config-if)#
R1(config-if)#
R1(config-if)#ip add 10.53.0.1 255.255.255.0
R1(config-if)#
R1(config-if)#do wr
Building configuration...
[OK]
```
```
R2(config)#int g0/1
R2(config-if)#
R2(config-if)#ip add 10.53.0.2 255.255.255.0
R2(config-if)#
```
Откройте окно конфигурации

b.	Перейдите в режим конфигурации маршрутизатора OSPF, используя идентификатор процесса 56.

```
R1(config)#
R1(config)#rout
R1(config)#router ospf 56
R1(config-router)#
```

c.	Настройте статический идентификатор маршрутизатора для каждого маршрутизатора (1.1.1.1 для R1, 2.2.2.2 для R2).

```
R1(config-router)#
R1(config-router)#rou
R1(config-router)#router-id 1.1.1.1
R1(config-router)#
```
```
R2(config)#
R2(config)#router osp
R2(config)#router ospf 56
R2(config-router)#
R2(config-router)#rou
R2(config-router)#router-id 2.2.2.2
R2(config-router)#
```

d.	Настройте инструкцию сети для сети между R1 и R2, поместив ее в область 0.

```
R2(config)#
R2(config)#int g0/1
R2(config-if)#
R2(config-if)#ip ospf 56 ar
R2(config-if)#ip ospf 56 area 0
R2(config-if)#
```

e.	Только на R2 добавьте конфигурацию, необходимую для объявления сети Loopback 1 в область OSPF area 0.

```
R2(config)#
R2(config)#int lo
R2(config)#int loopback 1

R2(config-if)#
%LINK-5-CHANGED: Interface Loopback1, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface Loopback1, changed state to up

R2(config-if)#
R2(config-if)#ip add 192.168.1.1 255.255.255.0
R2(config-if)#
R2(config-if)#ip ospf net
R2(config-if)#ip ospf network poin
R2(config-if)#ip ospf network point-to-point 
R2(config-if)#
R2(config-if)#
R2(config-if)#ip ospf 56 area 0
R2(config-if)#
R2(config-if)#do wr
Building configuration...
[OK]
R2(config-if)#
```

f.	Убедитесь, что OSPFv2 работает между маршрутизаторами. Выполните команду, чтобы убедиться, что R1 и R2 сформировали смежность.

```
R2#sh ip os ne


Neighbor ID     Pri   State           Dead Time   Address         Interface
1.1.1.1           1   FULL/BDR        00:00:31    10.53.0.1       G0/1
R2#
```
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

     10.0.0.0/8 is variably subnetted, 2 subnets, 2 masks
C       10.53.0.0/24 is directly connected, GigabitEthernet0/1
L       10.53.0.1/32 is directly connected, GigabitEthernet0/1
O    192.168.1.0/24 [110/101] via 10.53.0.2, 00:08:09, GigabitEthernet0/1

R1#
```

__Вопрос:__\
Какой маршрутизатор является DR? 

DR является маршрутизатор R2, на котором мы находимся сейчас.

Какой маршрутизатор является BDR? Каковы критерии отбора?

BDR является маршрутизатор R1, так как у него меньше Router ID

g.	На R1 выполните команду __show ip route ospf__, чтобы убедиться, что сеть __R2 Loopback1__ присутствует в таблице маршрутизации. 

```
R1#
R1#show ip route ospf
O    192.168.1.0 [110/101] via 10.53.0.2, 00:11:17, GigabitEthernet0/1

R1#
```

h.	Запустите Ping до адреса интерфейса __R2 Loopback 1__ из R1. Выполнение команды ping должно быть успешным.

```
R1#
R1#ping 192.168.1.1

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.1.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 0/0/1 ms

R1#
```

### Часть 3. Оптимизация и проверка конфигурации OSPFv2 для одной области

#### Шаг 1. Реализация различных оптимизаций на каждом маршрутизаторе.

a.	На R1 настройте приоритет OSPF интерфейса G0/1 на 50, чтобы убедиться, что R1 является назначенным маршрутизатором.

```
R1(config)#
R1(config)#int g0/1
R1(config-if)#
R1(config-if)#ip os pri
R1(config-if)#ip os priority 50
R1(config-if)#
R1#clear ip ospf pro
R1#clear ip ospf process 
Reset ALL OSPF processes? [no]: y

R1#
01:32:22: %OSPF-5-ADJCHG: Process 56, Nbr 2.2.2.2 on GigabitEthernet0/1 from FULL to DOWN, Neighbor Down: Adjacency forced to reset

01:32:22: %OSPF-5-ADJCHG: Process 56, Nbr 2.2.2.2 on GigabitEthernet0/1 from FULL to DOWN, Neighbor Down: Interface down or detached

R1#
01:32:27: %OSPF-5-ADJCHG: Process 56, Nbr 2.2.2.2 on GigabitEthernet0/1 from LOADING to FULL, Loading Done
```
Проверим состояние интерфейса

```
R1#sh ip os int

GigabitEthernet0/1 is up, line protocol is up
  Internet address is 10.53.0.1/24, Area 0
  Process ID 56, Router ID 1.1.1.1, Network Type BROADCAST, Cost: 100
  Transmit Delay is 1 sec, State DR, Priority 50
  Designated Router (ID) 1.1.1.1, Interface address 10.53.0.1
  Backup Designated Router (ID) 2.2.2.2, Interface address 10.53.0.2
  Timer intervals configured, Hello 10, Dead 40, Wait 40, Retransmit 5
    Hello due in 00:00:09
  Index 1/1, flood queue length 0
  Next 0x0(0)/0x0(0)
  Last flood scan length is 1, maximum is 1
  Last flood scan time is 0 msec, maximum is 0 msec
  Neighbor Count is 1, Adjacent neighbor count is 1
    Adjacent with neighbor 2.2.2.2  (Backup Designated Router)
  Suppress hello for 0 neighbor(s)
R1#
```

b.	Настройте таймеры OSPF на G0/1 каждого маршрутизатора для таймера приветствия, составляющего 30 секунд.

```
R1(config)#
R1(config)#int g0/1
R1(config-if)#
R1(config-if)#ip os hello-interval 30
R1(config-if)#ip os dead-interval 120
R1(config-if)#
R1(config-if)#
01:16:37: %OSPF-5-ADJCHG: Process 56, Nbr 2.2.2.2 on GigabitEthernet0/1 from FULL to DOWN, Neighbor Down: Dead timer expired

01:16:37: %OSPF-5-ADJCHG: Process 56, Nbr 2.2.2.2 on GigabitEthernet0/1 from FULL to DOWN, Neighbor Down: Interface down or detached

R1(config-if)#
R1(config-if)#ex
R1(config)#
```


c.	На __R1__ настройте статический маршрут по умолчанию, который использует интерфейс _Loopback 1_ в качестве интерфейса выхода. Затем распространите маршрут по умолчанию в OSPF. 

```
R1(config)#
R1(config)#int lo1
R1(config-if)#
R1(config-if)#ip add 172.16.1.1 255.255.255.0
R1(config-if)#
R1(config-if)#ex
R1(config)#ip route 0.0.0.0 0.0.0.0 loopback 1
%Default route without gateway, if not a point-to-point interface, may impact performance
R1(config)#
R1(config)#
R1(config)#rout
R1(config)#router osp
R1(config)#router ospf 56
R1(config-router)#
R1(config-router)#def
R1(config-router)#default-information orig
R1(config-router)#default-information originate 
R1(config-router)#
R1(config-router)#end
R1#
%SYS-5-CONFIG_I: Configured from console by console
```
Включим маршрутизацию на интерфейсе _Loopback 1_ в R1
```
R1(config)#int loopback 1
R1(config-if)# 
R1(config-if)#ip os 56 a 0
R1(config-if)#
R1(config-if)#ex
```

Обратите внимание на сообщение консоли после установки маршрута по умолчанию.

```
R1(config)#ip route 0.0.0.0 0.0.0.0 loopback 1
%Default route without gateway, if not a point-to-point interface, may impact performance
```
_Маршрут по умолчанию без шлюза, если это не интерфейс «точка-точка», может повлиять на производительность._

d.	добавьте конфигурацию, необходимую для OSPF для обработки __R2 Loopback 1__ как сети точка-точка.

```
R2(config)#
R2(config)#int lo1
R2(config-if)#
R2(config-if)#
R2(config-if)#ip ospf network point-to-point 
R2(config-if)#ex
R2(config)#
```

Это приводит к тому, что OSPF объявляет что _Loopback 1_ использует маску подсети интерфейса.

e.	Только на __R2__ добавьте конфигурацию, необходимую для предотвращения отправки объявлений OSPF в сеть _Loopback 1._

```
R2(config)#
R2(config)#rout
R2(config)#router os
R2(config)#router ospf 56
R2(config-router)#
R2(config-router)#pass
R2(config-router)#passive-interface loo
R2(config-router)#passive-interface loopback 1
R2(config-router)#
R2(config-router)#end
R2#
```

f.	Измените базовую пропускную способность для маршрутизаторов.

```
R2(config)#
R2(config)#rout
R2(config)#router os
R2(config)#router ospf 56
R2(config-router)#
R2(config-router)#au
R2(config-router)#auto-cost ref
R2(config-router)#auto-cost reference-bandwidth 10000
% OSPF: Reference bandwidth is changed.
        Please ensure reference bandwidth is consistent across all routers.
R2(config-router)#
```

 После этой настройки перезапустите OSPF с помощью команды __clear ip ospf process__. 
 
 ```
R2#
R2#clear ip ospf process
Reset ALL OSPF processes? [no]: y

R2#
02:05:49: %OSPF-5-ADJCHG: Process 56, Nbr 1.1.1.1 on GigabitEthernet0/1 from FULL to DOWN, Neighbor Down: Adjacency forced to reset

02:05:49: %OSPF-5-ADJCHG: Process 56, Nbr 1.1.1.1 on GigabitEthernet0/1 from FULL to DOWN, Neighbor Down: Interface down or detached

R2#
 ```
 Обратите внимание на сообщение консоли после установки новой опорной полосы пропускания.

 ```
R1#
02:08:18: %OSPF-5-ADJCHG: Process 56, Nbr 2.2.2.2 on GigabitEthernet0/1 from LOADING to FULL, Loading Done
R1#
 ```
 _Процесс 56, номер 2.2.2.2 на GigabitEthernet0/1 от ЗАГРУЗКИ до ПОЛНОЙ, загрузки завершен_

 Полоса пропускания влияет на стоимость прохождения маршрута. Так как мы установили полосу в 10 000 Мб/C, то стоимость по формуле должна быть 100. Проверим введя команду __show ip ospf interface__

 ```
R1#sh ip os interface 

GigabitEthernet0/1 is up, line protocol is up
  Internet address is 10.53.0.1/24, Area 0
  Process ID 56, Router ID 1.1.1.1, Network Type BROADCAST, Cost: 100
  Transmit Delay is 1 sec, State DR, Priority 50
  Designated Router (ID) 1.1.1.1, Interface address 10.53.0.1
  No backup designated router on this network
  Timer intervals configured, Hello 30, Dead 120, Wait 120, Retransmit 5
    Hello due in 00:00:05
  Index 1/1, flood queue length 0
  Next 0x0(0)/0x0(0)
  Last flood scan length is 1, maximum is 1
  Last flood scan time is 0 msec, maximum is 0 msec
  Neighbor Count is 0, Adjacent neighbor count is 0
  Suppress hello for 0 neighbor(s)
R1#
 ```
Все верн, стоимоть равня 100.


#### Шаг 2. Убедитесь, что оптимизация OSPFv2 реализовалась.

a.	Выполните команду __show ip ospf interface g0/1__ на __R1__ и убедитесь, что приоритет интерфейса установлен равным 50, а временные интервалы — Hello 30, Dead 120, а тип сети по умолчанию — Broadcast

```
R1#show ip ospf interface g0/1

GigabitEthernet0/1 is up, line protocol is up
  Internet address is 10.53.0.1/24, Area 0
  Process ID 56, Router ID 1.1.1.1, Network Type BROADCAST, Cost: 100
  Transmit Delay is 1 sec, State DR, Priority 50
  Designated Router (ID) 1.1.1.1, Interface address 10.53.0.1
  Backup Designated Router (ID) 2.2.2.2, Interface address 10.53.0.2
  Timer intervals configured, Hello 30, Dead 120, Wait 120, Retransmit 5
    Hello due in 00:00:12
  Index 1/1, flood queue length 0
  Next 0x0(0)/0x0(0)
  Last flood scan length is 1, maximum is 1
  Last flood scan time is 0 msec, maximum is 0 msec
  Neighbor Count is 1, Adjacent neighbor count is 1
    Adjacent with neighbor 2.2.2.2  (Backup Designated Router)
  Suppress hello for 0 neighbor(s)
R1#
```

b.	На __R1__ выполните команду __show ip route ospf__, чтобы убедиться, что сеть _R2 Loopback1_ присутствует в таблице маршрутизации.

```
R1#
R1#show ip route ospf
O    192.168.1.0 [110/101] via 10.53.0.2, 00:01:43, GigabitEthernet0/1

R1#
```

Обратите внимание на разницу в метрике между этим выходным и предыдущим выходным. Также обратите внимание, что маска теперь составляет 24 бита, в отличие от 32 битов, ранее объявленных.

c.	Введите команду __show ip route ospf__ на маршрутизаторе R2. Единственная информация о маршруте OSPF должна быть распространяемый по умолчанию маршрут R1.

```
R1#show ip route ospf
O    192.168.1.0 [110/101] via 10.53.0.2, 00:17:31, GigabitEthernet0/1

R1#
```

d.	Запустите Ping до адреса интерфейса __R1 Loopback 1__ из __R2__. Выполнение команды ping должно быть успешным.

Для начала проверим таблицу маршрутизации на R2


```
R2#sh ip route 
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2, E - EGP
       i - IS-IS, L1 - IS-IS level-1, L2 - IS-IS level-2, ia - IS-IS inter area
       * - candidate default, U - per-user static route, o - ODR
       P - periodic downloaded static route

Gateway of last resort is 10.53.0.1 to network 0.0.0.0

     10.0.0.0/8 is variably subnetted, 2 subnets, 2 masks
C       10.53.0.0/24 is directly connected, GigabitEthernet0/1
L       10.53.0.2/32 is directly connected, GigabitEthernet0/1
     172.16.0.0/24 is subnetted, 1 subnets
O       172.16.1.0/24 [110/101] via 10.53.0.1, 00:03:55, GigabitEthernet0/1
     192.168.1.0/24 is variably subnetted, 2 subnets, 2 masks
C       192.168.1.0/24 is directly connected, Loopback1
L       192.168.1.1/32 is directly connected, Loopback1
O*E2 0.0.0.0/0 [110/1] via 10.53.0.1, 00:12:21, GigabitEthernet0/1

R2#
```
Видим что маршрут по умолчанию существует, а так же видим роутер R1 по OSPF

Теперь запустим __ping__ на R2  в сторону R1

```
R2#
R2#ping 172.16.1.1

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 172.16.1.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 0/2/10 ms

R2#
```

