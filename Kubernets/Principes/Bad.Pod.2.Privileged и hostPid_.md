Единственное отличие от предыдущего варианта — способ получения root-доступа к хосту. Там мы делали `chroot` для файловой системы хоста, а здесь используем `nsenter`, чтобы получить права root для ноды, где запущен наш под.

- **Privileged**. Контекст безопасности `privileged: true` на уровне контейнера ломает почти любые преграды, которые по идее должен давать контейнер для защиты от атак. Остается разве что неймспейс PID. Без `hostPID` `nsenter` сможет входить только в неймспейсы процесса, который запущен в контейнере. Больше примеров действий, которые можно совершить с `privileged: true`, см. в следующем разделе — Проблемный под 3: только Privileged.
        
- **Privileged** + `hostPID`. Если одновременно задать `hostPID: true` и `privileged: true`, поду будут доступны все процессы на хосте, и мы сможем войти в систему `init` (PID 1) на хосте. А там уже можно запустить shell на ноде.

**Примеры использования**

  - [Твит Даффи Кули (Duffie Cooley) про Nsenter Pod](https://twitter.com/mauilion/status/1129468485480751104)
- [Неизведанный путь: атака на Kubernetes с параметрами по умолчанию с репозиторием](https://www.youtube.com/watch?v=HmoVSmTIOxM)
- [Node-shell Krew Plugin](https://github.com/kvaps/kubectl-node-shell)

[https://github.com/BishopFox/badPods/tree/main/manifests/priv-and-hostpid](https://github.com/BishopFox/badPods/tree/main/manifests/priv-and-hostpid)
