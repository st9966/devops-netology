1. Проверьте список доступных сетевых интерфейсов на вашем компьютере. Какие команды есть для этого в Linux и в Windows?
***Ответ***
В Linux:
```buildoutcfg
root@vagrant:/home/vagrant# ip link show
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP mode DEFAULT group default qlen 1000
    link/ether 08:00:27:73:60:cf brd ff:ff:ff:ff:ff:ff
```
В Windows:
```buildoutcfg
PS C:\Users\vasilev> ipconfig

Настройка протокола IP для Windows

Адаптер Ethernet Ethernet:

   DNS-суффикс подключения . . . . . : *******.ru
   Локальный IPv6-адрес канала . . . : fe80::a001:cd44:8c1f:4187%12
   IPv4-адрес. . . . . . . . . . . . : 192.168.0.234
   Маска подсети . . . . . . . . . . : 255.255.254.0
   Основной шлюз. . . . . . . . . : 192.168.0.1

Адаптер Ethernet VirtualBox Host-Only Network:

   DNS-суффикс подключения . . . . . :
   Локальный IPv6-адрес канала . . . : fe80::f5d3:9b2:4fc:d83f%19
   IPv4-адрес. . . . . . . . . . . . : 192.168.**.1
   Маска подсети . . . . . . . . . . : 255.255.255.0
```

2. Какой протокол используется для распознавания соседа по сетевому интерфейсу? Какой пакет и команды есть в Linux для этого?
***Ответ***
ARP протокол, arp-scan пакет.
arp-scan --interface=eth0 192.168.0.0/24
   
3. Какая технология используется для разделения L2 коммутатора на несколько виртуальных сетей? 
Какой пакет и команды есть в Linux для этого? Приведите пример конфига.
***Ответ***
Vlan
modprobe 8021q в linux 
```buildoutcfg
root@vagrant:/home/vagrant# ip link add link eth0 name eth0.10 type vlan id 10
root@vagrant:/home/vagrant# ip -d link show eth0.10
3: eth0.10@eth0: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000
    link/ether 08:00:27:73:60:cf brd ff:ff:ff:ff:ff:ff promiscuity 0 minmtu 0 maxmtu 65535
    vlan protocol 802.1Q id 10 <REORDER_HDR> addrgenmode eui64 numtxqueues 1 numrxqueues 1 gso_max_size 65536 gso_max_segs 65535
root@vagrant:/home/vagrant# ip addr add 192.168.1.200/24 brd 192.168.0.255 dev eth0.10
root@vagrant:/home/vagrant# ip link set dev eth0.10 up
root@vagrant:/home/vagrant# ip link show
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP mode DEFAULT group default qlen 1000
    link/ether 08:00:27:73:60:cf brd ff:ff:ff:ff:ff:ff
3: eth0.10@eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP mode DEFAULT group default qlen 1000
    link/ether 08:00:27:73:60:cf brd ff:ff:ff:ff:ff:ff
```
4. Какие типы агрегации интерфейсов есть в Linux? Какие опции есть для балансировки нагрузки? Приведите пример конфига.
***Ответ***
```buildoutcfg
root@vagrant:/home/vagrant# ip link add link eth0 name eth0.10 type vlan id 10
root@vagrant:/home/vagrant# ip link add link eth0 name eth0.20 type vlan id 20
root@vagrant:/home/vagrant# ip link add bond0 type bond mode 802.3ad
root@vagrant:/home/vagrant# ip link set eth0.10 master bond0
root@vagrant:/home/vagrant# ip link set eth0.20 master bond0
root@vagrant:/home/vagrant# ip link
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP mode DEFAULT group default qlen 1000
    link/ether 08:00:27:73:60:cf brd ff:ff:ff:ff:ff:ff
3: eth0.10@eth0: <BROADCAST,MULTICAST,SLAVE,UP,LOWER_UP> mtu 1500 qdisc noqueue master bond0 state UP mode DEFAULT group default qlen 1000
    link/ether 08:00:27:73:60:cf brd ff:ff:ff:ff:ff:ff
4: eth0.20@eth0: <BROADCAST,MULTICAST,SLAVE,UP,LOWER_UP> mtu 1500 qdisc noqueue master bond0 state UP mode DEFAULT group default qlen 1000
    link/ether 08:00:27:73:60:cf brd ff:ff:ff:ff:ff:ff
5: bond0: <BROADCAST,MULTICAST,MASTER> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000
    link/ether 08:00:27:73:60:cf brd ff:ff:ff:ff:ff:ff
```
5. Сколько IP адресов в сети с маской /29 ? Сколько /29 подсетей можно получить из сети с маской /24. 
Приведите несколько примеров /29 подсетей внутри сети 10.10.10.0/24.
***Ответ***
Сколько IP адресов в сети с маской /29
Количество доступных адресов	8
Количество рабочих адресов для хостов	6
Сколько /29 подсетей можно получить из сети с маской /24
32 подсети с маской /29
Приведите несколько примеров /29 подсетей внутри сети 10.10.10.0/24
10.10.10.1-8
10.10.10.20-28
10.10.10.30-38

6. Задача: вас попросили организовать стык между 2-мя организациями. 
Диапазоны 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16 уже заняты. Из какой подсети допустимо взять частные IP адреса? 
Маску выберите из расчета максимум 40-50 хостов внутри подсети.
***Ответ***
100.64.0.0/26

7. Как проверить ARP таблицу в Linux, Windows? Как очистить ARP кеш полностью? 
Как из ARP таблицы удалить только один нужный IP?
***Ответ***
win:
```buildoutcfg
PS C:\Users\vasilev> arp -a
PS C:\Users\vasilev> netsh interface ip delete arpcache
PS C:\Users\vasilev> arp -d 192.168.0.25
```
Linux:
```buildoutcfg
root@vagrant:/home/vagrant# apt-get install net-tools
root@vagrant:/home/vagrant# arp -a
? (10.0.2.3) at 52:54:00:12:35:03 [ether] on eth0
_gateway (10.0.2.2) at 52:54:00:12:35:02 [ether] on eth0
root@vagrant:/home/vagrant# arp -d 10.0.2.2
root@vagrant:/home/vagrant# ip -s -s neigh flush all
```