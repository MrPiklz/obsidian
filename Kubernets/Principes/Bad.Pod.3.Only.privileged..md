С `privileged: true` у нас два пути:
[https://github.com/BishopFox/badPods/tree/main/manifests/priv](https://github.com/BishopFox/badPods/tree/main/manifests/priv)


  

- **Примонтировать файловую систему хоста.** В привилегированном режиме `/dev` на хосте будет доступен нашему поду. Мы можем примонтировать к нашему поду диск с файловой системой хоста командой `mount`. По моему опыту, полной свободы действий нам это не даст. Некоторые файлы и пути эскалации привилегий недоступны из привилегированного пода, если только мы не эскалируем его до полноценной shell. Зато можно легко примонтировать диск и посмотреть, что мы там увидим.
- **Использовать уязвимость cgroup в user mode с помощью специальных программ.** Лучше всего получить интерактивный root-доступ к ноде, но тут придется повозиться. Можем использовать прототип эксплойта Феликса Уилхелма (Felix Wilhelm) [undock.sh](https://twitter.com/_fel1x/status/1151487051986087936), чтобы выполнять по команде за раз, или вариант, который Брэндон Эдвадрс (Brandon Edwards) и Ник Фриман (Nick Freeman) предложили в своем выступлении [Краткое руководство по выходу из контейнера](https://www.youtube.com/watch?v=BQlqita2D2s). В этом варианте мы заставляем хост подключиться к listener на поде для эскалации до интерактивного root-доступа на хосте. Еще один вариант — взять модуль Metasploit [Docker Privileged Container Escape](https://www.rapid7.com/db/modules/exploit/linux/local/docker_privileged_container_escape/), где используется тот же эксплойт для апгрейда shell на контейнере до shell на хосте.


- [Экслойт Феликса Уилхелма Cgroup Usermode Helper](https://twitter.com/_fel1x/status/1151487051986087936)
- [Обзор Container Escape в Docker](https://blog.trailofbits.com/2019/07/19/understanding-docker-container-escapes/)
- [Краткое руководство по выходу из контейнера](https://www.youtube.com/watch?v=BQlqita2D2s)
- [Модуль Metasploit Docker Privileged Container Escape](https://www.youtube.com/watch?v=BQlqita2D2s)