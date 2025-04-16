https://github.com/BishopFox/badPods/tree/main/manifests/everything-allowed

Созданный под монтирует файловую систему хоста к поду. Проще всего будет создать под в ноде control-plane с помощью селектора [nodeName](https://kubernetes.io/docs/tasks/configure-pod-container/assign-pods-nodes/#https://kubernetes.io/docs/tasks/configure-pod-container/assign-pods-nodes/) в манифесте. Потом можно выполнить в поде `exec` и сделать `chroot` для каталога, где примонтирована файловая система хоста. Теперь у нас есть права root в ноде, где запущен наш под.

  

- Читаем секреты из `etcd`. Если мы запускаем под на ноде control-plane с помощью селектора `nodeName` в спецификации пода, мы легко получаем доступ к базе данных `etcd`, где хранится вся конфигурация кластера, включая секреты.
- Ищем токены привилегированных учеток сервисов. Даже если удалось запустить под только на рабочей ноде, мы все равно можем получить доступ к секретам всех подов на этой ноде. В продакшен-кластере даже на рабочей ноде всегда есть хотя бы один под с токеном, который привязан к учетке сервиса, связанной с clusterrolebinding, так что мы можем, например, создавать поды или просматривать секреты во всех неймспейсах.

  

Больше схем эскалации привилегий см. в файле [README](https://github.com/BishopFox/badPods/tree/main/manifests/everything-allowed) (ниже) и в разделе Проблемный под 4: hostPath.

  

**Примеры использования**

  

[https://github.com/BishopFox/badPods/tree/main/manifests/everything-allowed](https://github.com/BishopFox/badPods/tree/main/manifests/everything-allowed)

  

**Дополнительные материалы**

  

- [Самая бестолковая команда Kubernetes на свете](https://raesene.github.io/blog/2019/04/01/The-most-pointless-kubernetes-command-ever/)
- [Защита Kubernetes — KubeCon NA 2019 CTF](https://securekubernetes.com/)
- [Подробный обзор угроз для Kubernetes в реальном мире](https://research.nccgroup.com/2020/02/12/command-and-kubectl-talk-follow-up/)
- [Компрометация кластера Kubernetes с помощью разрешений RBAC (слайды)](https://www.youtube.com/watch?v=1LMo0CftVC4)
- [Неизведанный путь: атака на Kubernetes с параметрами по умолчанию с репозиторием](https://www.youtube.com/watch?v=1LMo0CftVC4)
- 