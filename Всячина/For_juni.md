https://telegra.ph/Zadachi-dlya-DevOps-Junior-01-21
https://github.com/Swfuse/devops-interview?ysclid=m9dr5kgne5684059892
https://selectel.ru/blog/tutorials/helm-charts-kubernetes/?ysclid=m9du593slo106598454
https://habr.com/ru/articles/733158/
https://itvdn.com/ru/blog/article/300-devops
https://github.com/cardinalit/TTDevOps?ysclid=m9dr1jfpa0182074472  !!!
!!!

https://tproger.ru/articles/nebolshaja-podborka-testovyh-zadanij-dlja-junior-devops-inzhenera?ysclid=m9dr1ccnvt485839681



1. Какой командой посмотреть текущие процессы в системе?
— ps — просто вывод.
— ps -ef — вывод с подробной инфой.
— ps aux — вывод с подробной инфой по всем юзерам.
— top/htop — вывод процессов в реальном времени.
2. Как узнать, сколько свободного места на диске?
— df -h
3. Как посмотреть последние 10 строк файла?
— tail -n 10 имя_файла
4. Как узнать, на каком порту слушает процесс?
— netstat -tulnp
— lsof -i -P -n
—  ss -tuln
потом ищем нужный PID в выводе
5. Как посмотреть переменные окружения?
— printenv или env
6. Как посмотреть все открытые сетевые соединения?
— netstat -tunap
— ss -tunap
7. Как узнать, сколько памяти сейчас используется в системе?
— free -h — показывает использование оперативной памяти.
— top или htop.

dmesg покажет только сообщения ядра с момента загрузки.
А journalctl -k -b -1 покажет логи ядра предыдущего ребута, что критично при расследовании падений.

netstat устарел и медленнее.
ss -tunap даст быстрее и подробнее информацию о соединениях и сокетах.

Всегда включай `bash`-историю с таймстемпами
Добавь в .bashrc:
export HISTTIMEFORMAT="%F %T "

Zombie
ps -eo pid,ppid,state,cmd | grep ' Z '
