Вы запускаете контейнеризированный сервис (например, через Docker или systemd-nspawn) и устанавливаете лимит по памяти с помощью cgroup v2:


echo 500M > /sys/fs/cgroup/myservice/memory.max

Сервис стартует нормально, но через некоторое время сервер начинает тормозить, а мониторинг показывает, что суммарное использование памяти превышает лимиты.

При этом внутри контейнера или процесса всё выглядит нормально — top`/`htop внутри контейнера показывают использование памяти в рамках лимита.

Почему сервер «съедает» больше памяти, чем установлено? Как это диагностировать и объяснить?

Контейнер активно использует tmpfs (например, для кэша), и у вас включён swap.

Многие админы полагают, что установка лимита через cgroup (особенно memory.max в cgroup v2) гарантирует, что ВСЁ потребление памяти процесса будет лимитировано. Но это не всегда так.


 tmpfs и cgroups:

- tmpfs (например, /dev/shm, временные каталоги контейнера) не всегда учитывается в ограничении по памяти cgroup.  
- По умолчанию tmpfs монтируется в пределах общей системы и может «выходить за лимит», если mount options не были заданы корректно.

 Page Cache:

- Даже если процесс работает внутри лимита по памяти, его файловый ввод/вывод (например, большие логи) может накапливать кэш в page cache на уровне ядра, что не всегда учитывается cgroup как «реальное» использование процесса.


 Swap и cgroup:

- Если swap включён, процессы могут использовать его даже после достижения лимита, что позволяет системе «обходить» установленный лимит (в старых версиях ядра). В cgroup v2 это стало лучше, но всё ещё возможны нюансы при комбинировании swap и tmpfs.



 Как проверить:

1️⃣ Посмотреть, кто реально держит память:

```bash
cat /sys/fs/cgroup/myservice/memory.current
```

2️⃣ Проверить tmpfs внутри cgroup:

```bash
findmnt -o TARGET,SIZE,USED -t tmpfs
```

3️⃣ Проверить общий кэш ядра:

```bash
free -h
```

4️⃣ Посмотреть по cgroup-метрикам через cat /proc/<pid>/cgroup.

---

🛠 Как исправить:

- При монтировании tmpfs указывать опцию size в пределах лимита или использовать монтирование tmpfs внутри cgroup, чтобы оно считалось частью лимита:

```bash
mount -t tmpfs -o size=100M tmpfs /mycontainer/tmp
```

- Для Docker-контейнеров использовать флаг:

```bash
--tmpfs /tmp:size=100m
```

- Обновить ядро до последней версии, где cgroup v2 лучше работает с tmpfs и swap.

---

✅ Вывод:

• cgroups контролируют процессы, но не всегда все связанные ресурсы (особенно tmpfs и page cache).  
• Даже если процесс «видит» ограничение памяти, система может выйти за пределы лимита из-за особенностей кэширования и tmpfs.  
• Для строгого контроля нужно явно указывать размеры tmpfs и следить за page cache при мониторинге.

💡 Бонус-вопрос:  
Как можно очистить page cache без перезагрузки, если у вас на сервере критическая нехватка памяти?
Подсказка: echo 3 > /proc/sys/vm/drop_caches 