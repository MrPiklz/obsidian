**ifconfig**
 -a Доступные
 $ sudo ifconfig eth0 192.168.56.5 netmask 255.255.255.0
 $ sudo ifconfig up eth0
 $ sudo ifconfig down eth0

**ifup**\$ sudo ifdown eth0
**ifquery** - analyz


route
$ sudo route add default gw   \\Добавить шлюз по умолчанию в таблицу маршрутизации
$ sudo route add -net  gw \\\Добавить сетевой маршрут в таблицу маршрутизации:
$ sudo route del -net  -удалить конкретную запись


netstat -tnlp
netsat -r


df

jobs

Режим	Число
rwx     7
rw-     6
r-x     5
r--     4
-wx     3
-w-     2
--x     1
---     0



ps -eo pid,ppid,state,cmd | grep ' Z '    --- zombie processes