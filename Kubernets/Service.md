Когда мы создаем Service, мы указываем в нем, какие именно Поды он должен "обслуживать", для этого в спеке Сервиса мы прописываем **selector**:

```yaml
spec:
selector:
  app: goapp
```

Этот селектор определяет, к каким именно Подам будет направлен трафик. В Подах указываются **лейблы**, и если лейблы Подов совпадают с селектором сервиса, трафик пойдет на эти Поды.

```yaml
...
  template:
    metadata:
      labels:
        app: goapp
...
```
### Основные типы Сервисов:

1. **ClusterIP** (по умолчанию)**доступ извне кластера отсутствует**
2. **NodePort** открывает доступ к приложению через конкретный порт на всех нодах кластера
3. **LoadBalancer** для подключения из внешнего мира к вашему приложению.
4. **ExternalName** (используется крайне редко, поэтому не будем лишний раз забивать голову)
5. Создадим папку `demo-clusterIP`, в этой папке разместим манифесты.
6. 01_ns.yaml



    ```yaml
    apiVersion: v1
    kind: Namespace
    metadata:
      name: demo-clusterip
    ```
    
7. 02_deployment.yaml
    
    ```yaml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: goapp-deployment
      namespace: demo-clusterip
      labels:
        app: goapp
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: goapp
      template:
        metadata:
          labels:
            app: goapp
        spec:
          containers:
          - name: web
            image: ksxack/lesson1:v0.2
            ports:
            - containerPort: 8080
    ```
    
8. 03_service.yaml
    
    ```yaml
    apiVersion: v1
    kind: Service
    metadata:
      namespace: demo-clusterip
      name: goweb-svc
    spec:
      selector:
        app: goapp
      ports:
        - protocol: TCP
          port: 80         ## порт, который слушает Сервис
          targetPort: 8080 ## порт Пода, на который Сервис направляет запросы
    ```
    
9. Применим манифесты:
    
    ```bash
    kubectl apply -f demo-clusterIP/
    ```


## port-forward

Для того, чтобы проверить, что Service настроен правильно, мы можем посмотреть`describe`:

1. Запустите команду: 
    
    ```bash
    kubectl -n demo-clusterip describe svc goweb-svc
    ```
    
2. Если в поле Endpoints стоит 1 или несколько IP адресов (это IP Подов), то лейблы и селектор настроены верно.


```bash
    kubectl -n demo-clusterip port-forward svc/goweb-svc 8181:80
    ```
    