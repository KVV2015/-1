# Лабораторная работа. Доступ к сетевым устройствам по протоколу SSH.

### Топология сети

[![2024-04-14-19-42-53.png](https://i.postimg.cc/sDRs7K3B/2024-04-14-19-42-53.png)](https://postimg.cc/9z16jdRV)

### Таблица адресации
|     <br>Устройство     	|     <br>Интерфейс     	|     <br>IP-адрес       	|     <br>Маска    подсети     	|     <br>Шлюз    по умолчанию     	|
|------------------------	|-----------------------	|------------------------	|------------------------------	|----------------------------------	|
|    <br>R1              	|    <br>G0/0/1         	|    <br>192.168.1.1     	|    <br>255.255.255.0         	|    <br>—                         	|
|    <br>S1              	|    <br>VLAN 1         	|    <br>192.168.1.11    	|    <br>255.255.255.0         	|    <br>192.168.1.1               	|
|    <br>PC-A            	|    <br>NIC            	|    <br>192.168.1.3     	|    <br>255.255.255.0         	|    <br>192.168.1.1               	|

## Задачи

  1. Настроить основные параметры устройств
  2. Настройка маршрутизатора для доступа по протоколу SSH
  3. Настройка коммутатора для доступа по протоколу SSH
  4. SSH через интерфейс командной строки CLI коммутатора


## Сценарий

Раньше для удаленной настройки сетевых устройств в основном применялся протокол Telnet. Однако он не обеспечивает шифрование информации, передаваемой между клиентом и сервером, что позволяет анализаторам сетевых пакетов перехватывать пароли и данные конфигурации.
Secure Shell (SSH) — это сетевой протокол, устанавливающий безопасное подключение с эмуляцией терминала к маршрутизатору или иному сетевому устройству. Протокол SSH шифрует все сведения, которые поступают по сетевому каналу, и предусматривает аутентификацию удаленного компьютера. Протокол SSH все больше заменяет Telnet — именно его выбирают сетевые специалисты в качестве средства удаленного входа в систему. SSH чаще всего используется для входа на удаленное устройство и выполнения команд. Но это может также передавать файлы по связанным протоколам SFTP или SCP

Чтобы протокол SSH мог работать, на сетевых устройствах, взаимодействующих между собой, должна быть настроена поддержка SSH. В данной работе необходимо включить SSH-сервер на маршрутизаторе, после чего подключиться к этому маршрутизатору, используя ПК с установленным клиентом SSH. В локальной сети подключение обычно устанавливается с помощью Ethernet и IP.

## Решение

### Часть 1. Часть 1. Настройка основных параметров устройств
В части 1 потребуется настроить топологию сети и основные параметры, такие как IP-адреса интерфейсов, доступ к устройствам и пароли на маршрутизаторе.

#### Шаг 1. Создадим сеть согласно топологии.
#### Шаг 2. Выполним инициализацию и перезагрузку маршрутизатора и коммутатора.
#### Шаг 3. Настроим маршрутизатор

a.	Подключитесь к маршрутизатору с помощью консоли и активируйте привилегированный режим EXEC.

 b.	Войдите в режим конфигурации.

 c.	Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.

 d.	Назначьте class в качестве зашифрованного пароля привилегированного режима EXEC.

 e.	Назначьте cisco в качестве пароля консоли и включите вход в систему по паролю.

 f.	Назначьте cisco в качестве пароля VTY и включите вход в систему по паролю.

 g.	Зашифруйте открытые пароли.

 h.	Создайте баннер, который предупреждает о запрете несанкционированного доступа.

 i.	Настройте и активируйте на маршрутизаторе интерфейс G0/0/1, используя информацию, приведенную в таблице адресации.

 j.	Сохраните текущую конфигурацию в файл загрузочной конфигурации.

```
outer>
Router>en
Router#
Router#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Router(config)#
Router(config)#
Router(config)#no ip domain-lookup
Router(config)#
Router(config)#enabl
Router(config)#enable secr
Router(config)#enable secret class
Router(config)#
Router(config)#line con
Router(config)#line console 0
Router(config-line)#pass
Router(config-line)#password cisco
Router(config-line)#
Router(config-line)#login
Router(config-line)#end
Router#
%SYS-5-CONFIG_I: Configured from console by console

Router#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Router(config)#line vty 0 4
Router(config-line)#
Router(config-line)#pass
Router(config-line)#password cisco
Router(config-line)#
Router(config-line)#login
Router(config-line)#
Router(config-line)#^Z
Router#
%SYS-5-CONFIG_I: Configured from console by console

Router#
Router#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Router(config)#serv
Router(config)#service pass
Router(config)#service password-encryption 
Router(config)#
Router(config)#banner motd @++++NO PUT ENTER++++++@
Router(config)#
Router(config)#hostR!
                   ^
% Invalid input detected at '^' marker.
	
Router(config)#
Router(config)#host R1
R1(config)#
R1(config)#inter
R1(config)#interface g 0/1
R1(config-if)#
R1(config-if)#ip add
R1(config-if)#ip address 192.168.1.1 255.255.255.0
R1(config-if)#
R1(config-if)#no sh
R1(config-if)#no shutdown 

R1(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet0/1, changed state to up

R1(config-if)#do wr
R1(config-if)#do wr
Building configuration...
[OK]
R1(config-if)#^Z
R1#
%SYS-5-CONFIG_I: Configured from console by console

R1#
```
#### Шаг 4. Настройте PC-A

[![2024-04-14-22-18-25.png](https://i.postimg.cc/7ZrbVc20/2024-04-14-22-18-25.png)](https://postimg.cc/1fM9RvM3)

#### Шаг 5. Проверьте подключение к сети

Пошлите с PC-A команду Ping на маршрутизатор R1. Если эхо-запрос с помощью команды ping не проходит, найдите и устраните неполадки подключения.

```
C:\>ping 192.168.1.1

Pinging 192.168.1.1 with 32 bytes of data:

Reply from 192.168.1.1: bytes=32 time=1ms TTL=255
Reply from 192.168.1.1: bytes=32 time=1ms TTL=255
Reply from 192.168.1.1: bytes=32 time<1ms TTL=255
Reply from 192.168.1.1: bytes=32 time=1ms TTL=255

Ping statistics for 192.168.1.1:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 1ms, Average = 0ms

C:\>
```
### Часть 2. Настройка маршрутизатора для доступа по протоколу SSH.


Подключение к сетевым устройствам по протоколу Telnet сопряжено с риском для безопасности, поскольку вся информация передается в виде открытого текста. Протокол SSH шифрует данные сеанса и обеспечивает аутентификацию устройств, поэтому для удаленных подключений рекомендуется использовать именно этот протокол. В части 2 вам нужно настроить маршрутизатор для приема соединений SSH по линиям VTY


#### Шаг 1 Настройте аутентификацию устройств.

При генерации ключа шифрования в качестве его части используются имя устройства и домен. Поэтому эти имена необходимо указать перед вводом команды crypto key.
Откройте окно конфигурации

a.	Задайте имя устройства.

b.	Задайте домен для устройства.


```
R1(config)#ip domain-n
R1(config)#ip domain-name vit.home
R1(config)#
```

#### Шаг 2 Создайте ключ шифрования с указанием его длины.

```
R1(config)#
R1(config)#cryp
R1(config)#crypto key gen
R1(config)#crypto key generate rsa
The name for the keys will be: R1.vit.home
Choose the size of the key modulus in the range of 360 to 2048 for your
  General Purpose Keys. Choosing a key modulus greater than 512 may take
  a few minutes.

How many bits in the modulus [512]: 1024
% Generating 1024 bit RSA keys, keys will be non-exportable...[OK]

R1(config)#
*Mar 1 0:33:50.59: %SSH-5-ENABLED: SSH 1.99 has been enabled
R1(config)#
R1(config)#ip ssh vers
R1(config)#ip ssh version 2
R1(config)#
```
#### Шаг 3 Создайте имя пользователя в локальной базе учетных записей.
Настройте имя пользователя, используя admin в качестве имени пользователя и __Adm1nP @55__ в качестве пароля
```
R1(config)#
R1(config)#usernam
R1(config)#username admin priv
R1(config)#username admin privilege 15 secret Adm1nP @55
R1(config)#
```
#### Шаг 4 Активируйте протокол SSH на линиях VTY.

a.	Активируйте протоколы Telnet и SSH на входящих линиях VTY с помощью команды __transport input__.

b.	Измените способ входа в систему таким образом, чтобы использовалась проверка пользователей по локальной базе учетных записей.

```
R1(config)#
R1(config)#line vty 0 4
R1(config-line)#
R1(config-line)#login local
R1(config-line)#
R1(config-line)#transport input
R1(config-line)#transport input ssh
R1(config-line)#
R1(config-line)#
```

#### Шаг 5 Сохраните текущую конфигурацию в файл загрузочной конфигурации.

```
R1(config-line)#^Z
R1#
%SYS-5-CONFIG_I: Configured from console by console

R1#write
Building configuration...
[OK]
R1#
```

#### Шаг 6 Установите соединение с маршрутизатором по протоколу SSH

a.	Запустите Tera Term с PC-A.

[![2024-04-14-22-38-58.png](https://i.postimg.cc/GpFy1Rkc/2024-04-14-22-38-58.png)](https://postimg.cc/p9dTDgG7)

b.	Установите SSH-подключение к R1. Use the username admin and password Adm1nP@55. У вас должно получиться установить SSH-подключение к R1

[![2024-04-14-22-42-27.png](https://i.postimg.cc/KzdjKs79/2024-04-14-22-42-27.png)](https://postimg.cc/XGk4mLzF)


### Часть 3. Настройка коммутатора для доступа по протоколу SSH.

#### Шаг 1 Настройте основные параметры коммутатора.

a.	Подключитесь к коммутатору с помощью консольного подключения и активируйте привилегированный режим EXEC.

b.	Войдите в режим конфигурации.

c.	Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.

d.	Назначьте class в качестве зашифрованного пароля привилегированного режима EXEC.

e.	Назначьте cisco в качестве пароля консоли и включите вход в систему по паролю.

f.	Назначьте cisco в качестве пароля VTY и включите вход в систему по паролю.

g.	Зашифруйте открытые пароли.

h.	Создайте баннер, который предупреждает о запрете несанкционированного доступа.

i.	Настройте и активируйте на коммутаторе интерфейс VLAN 1, используя информацию, приведенную в таблице адресации.

j.	Сохраните текущую конфигурацию в файл загрузочной конфигурации.

```
Switch>
Switch>en
Switch#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#
Switch(config)#host S1
S1(config)#
S1(config)#
S1(config)#no ip do
S1(config)#no ip domain-n
S1(config)#no ip domain-loocup
                           ^
% Invalid input detected at '^' marker.
	
S1(config)#no ip domain-lookup
S1(config)#
S1(config)#enabl
S1(config)#enable secret class
S1(config)#
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

S1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
S1(config)#
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

S1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
S1(config)#
S1(config)#serv
S1(config)#service pas
S1(config)#service password-encryption 
S1(config)#
S1(config)#banner motd @+++++NO PUT ENTER+++++@
S1(config)#
S1(config)#do write 
Building configuration...
[OK]
S1(config)#
```

#### Шаг 2 Настройте коммутатор для соединения по протоколу SSH.

Для настройки протокола SSH на коммутаторе используйте те же команды, которые применялись для аналогичной настройки маршрутизатора в части 2.

a.	Настройте имя устройства, как указано в таблице адресации.

b.	Задайте домен для устройства.

c.	Создайте ключ шифрования с указанием его длины.

d.	Создайте имя пользователя в локальной базе учетных записей.

e.	Активируйте протоколы Telnet и SSH на линиях VTY.

f.	Измените способ входа в систему таким образом, чтобы использовалась проверка пользователей по локальной базе учетных записей.

```
S1(config)#
S1(config)#ip domain-n
S1(config)#ip domain-name vit.home
S1(config)#
S1(config)#cript
S1(config)#crypto key
S1(config)#crypto key gener
S1(config)#crypto key generate rsa
The name for the keys will be: S1.vit.home
Choose the size of the key modulus in the range of 360 to 2048 for your
  General Purpose Keys. Choosing a key modulus greater than 512 may take
  a few minutes.

How many bits in the modulus [512]: 1024
% Generating 1024 bit RSA keys, keys will be non-exportable...[OK]

S1(config)#
*Mar 1 0:9:8.587: %SSH-5-ENABLED: SSH 1.99 has been enabled
S1(config)#ip ssh ver
S1(config)#ip ssh version 2
S1(config)#
S1(config)#username admin priv
S1(config)#username admin privilege 15 secr
S1(config)#username admin privilege 15 secret cisco
S1(config)#
S1(config)#line vty 0 4
S1(config-line)#
S1(config-line)#login local
S1(config-line)#
S1(config-line)#transp
S1(config-line)#transport inp
S1(config-line)#transport input ssh
S1(config-line)#
S1(config-line)#end
S1#
%SYS-5-CONFIG_I: Configured from console by console

S1#wr
S1#write 
Building configuration...
[OK]
S1#
```

#### Шаг 3. Установите соединение с коммутатором по протоколу SSH

Запустите программу Tera Term на PC-A, затем установите подключение по протоколу SSH к интерфейсу SVI коммутатора S1

[![2024-04-14-22-59-54.png](https://i.postimg.cc/Ss7CcMVF/2024-04-14-22-59-54.png)](https://postimg.cc/WFzdvtQX)


[![2024-04-14-22-59-11.png](https://i.postimg.cc/qM7yCg3S/2024-04-14-22-59-11.png)](https://postimg.cc/bdKs7yL9)

Связь с коммутатором установленна.

### Часть 4. Настройка  протокола SSH с использованием интерфейса командной строки (CLI) коммутатора

Клиент SSH встроен в операционную систему Cisco IOS и может запускаться из интерфейса командной строки. В части 4 вам предстоит установить соединение с маршрутизатором по протоколу SSH, используя интерфейс командной строки коммутатора.


#### Шаг 1 Посмотрите доступные параметры для клиента SSH в Cisco IOS.

Используйте вопросительный знак (?), чтобы отобразить варианты параметров для команды ssh

```
S1#ssh ?
  -l  Log in using this user name
  -v  Specify SSH Protocol Version
S1#ssh 
```
#### Шаг 2  Установите с коммутатора S1 соединение с маршрутизатором R1 по протоколу SSH.


  a. Чтобы подключиться к маршрутизатору R1 по протоколу SSH, введите команду __–l admin__. Это позволит вам войти в систему под именем admin. При появлении приглашения введите в качестве пароля __Adm1nP@55__

```
S1#
S1#ssh -l admin 192.168.1.1

Password: 


++++++NO PUT ENTER++++

R1#
R1#
```
b.	Чтобы вернуться к коммутатору S1, не закрывая сеанс SSH с маршрутизатором R1, нажмите комбинацию клавиш Ctrl+Shift+6. Отпустите клавиши Ctrl+Shift+6 и нажмите x. Отображается приглашение привилегированного режима EXEC коммутатора
```
R1#
R1#" 
S1#
```
c.	Чтобы вернуться к сеансу SSH на R1, нажмите клавишу Enter в пустой строке интерфейса командной строки. Чтобы увидеть окно командной строки маршрутизатора, нажмите клавишу Enter еще раз.
```
S1#
[Resuming connection 1 to 192.168.1.1 ... ]

Translating " "
% Unknown command or computer name, or unable to find computer address

R1#
```

d.	Чтобы завершить сеанс SSH на маршрутизаторе R1, введите в командной строке маршрутизатора команду exit.
```
R1#
R1#
R1#exit

[Connection to 192.168.1.1 closed by foreign host]
S1#
```

__Примечание__ Для использования командной строки коммутатора требуется использовать SSH версии 2.

