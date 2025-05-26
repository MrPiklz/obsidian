```
sudo cat /proc/3813/oom_score
```

sudo echo -100 > /proc/3813/oom_score_adj

Чтобы задать значение `oom_score_adj`, установите OOMScoreAdjust в блоке сервиса:
```
[Service]
OOMScoreAdjust=-1000
```

Или используйте `oomprotect` в команде `rcctl`.
```
rcctl set <i>servicename</i> oomprotect -1000
```
включить
sudo -s sysctl -w vm.oom-kill = 1


ще один способ включения и отключения — написать переменную `panic_on_oom`. Значение всегда можно проверить в `/proc`.
```
$ cat /proc/sys/vm/panic_on_oom
0
```
Если установить значение 0, то когда закончится память, kernel panic не будет.
```
$ echo 0 > /proc/sys/vm/panic_on_oom
```
Если установить значение 1, то когда закончится память, случится kernel panic.