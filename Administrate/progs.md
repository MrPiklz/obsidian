
проверка сетевых проблемс
```
 iperf -c 10.0.0.3
```

Проблема с дисками/ проверка скорости записи
чтение
```
hdparm -Tt /dev/sda
```
```
hdparm -Tt /dev/xvda5
```

Скорость записи
```
sync; dd if=/dev/zero of=/var/lib/redis/tempfile bs=1M count=5024; sync
```