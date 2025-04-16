Даже если все атрибуты запрещены, мы все равно можем найти множество путей атак, если можно создать под или хотя бы получить к нему доступ. Имея доступ к поду Kubernetes, обращаем внимание на следующее:

  

- **Доступные облачные метаданные**. Если под размещен в облаке, пытаемся получить доступ к облачному сервису метаданных. Там можно найти учетные данные IAM, связанные с нодой, или хотя бы облачные учетные данные IAM, созданные специально для этого пода. В обоих случаях можно добраться до кластера, до облачной среды или и туда, и туда.
- **Учетки сервисов с лишними разрешениями.** Если учетка сервиса по умолчанию в пространстве имен примонтирована к `/var/run/secrets/kubernetes.io/serviceaccount/token` в поде и позволяет слишком многое, с этим токеном можно эскалировать привилегии в кластере.
- **Компоненты Kubernetes с неправильной конфигурацией.** Если у apiserver или [kubelet'ов для anonymous-auth задано](https://labs.f-secure.com/blog/attacking-kubernetes-through-kubelet/) `true` и никакие сетевые политики нам не мешают, можно взаимодействовать с ними напрямую без аутентификации.
- **Эксплойты в ядре, движке контейнера или Kubernetes.** Эксплойт в этих компонентах можно использовать для выхода из контейнера или доступа к кластеру Kubernetes без дополнительных разрешений.
- **Уязвимые сервисы.** Если смотреть на сетевые сервисы кластера через под, скорее всего, мы увидим больше, чем с машины, где создали этот под. Можем поискать уязвимые сервисы и приложения, направляя трафик через под.

  

**Примеры использования**  
[https://github.com/BishopFox/badPods/tree/main/manifests/nothing-allowed](https://github.com/BishopFox/badPods/tree/main/manifests/nothing-allowed)

  

**Дополнительные материалы**

  

[Защита Kubernetes — KubeCon NA 2019 CTF](https://labs.f-secure.com/blog/attacking-kubernetes-through-kubelet/)  
[Kubernetes Goat](https://madhuakula.com/kubernetes-goat/)  
[Атака на Kubernetes через Kubelet](https://labs.f-secure.com/blog/attacking-kubernetes-through-kubelet/)  
[Подробный обзор угроз для Kubernetes в реальном мире](https://research.nccgroup.com/2020/02/12/command-and-kubectl-talk-follow-up/)  
[Краткое руководство по выходу из контейнера](https://www.youtube.com/watch?v=BQlqita2D2s)  
[CVE-2020-8558 POC](https://github.com/tabbysable/POC-2020-8558)