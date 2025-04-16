монтирование файлов config.yaml (или любых других) к уже запущенным Контейнерам в Подах Kubernetes.


### Создаем ConfigMap

Давайте настроим ресурс вида ConfigMap и примонтируем его к Контейнеру в Поде.   
Сначала создайте YAML-манифесты:

1. ns.yaml
    
    ```yaml
    apiVersion: v1
    kind: Namespace
    metadata:
      name: demo-cm
    ```
    
2. configmap.yaml
    
    ```yaml
    apiVersion: v1
    kind: ConfigMap
    metadata:
      name: first-cm
      namespace: demo-cm
    data:
      config.yaml: |
        log_level: "debug"
        database_address: 156.23.15.2
    ```


Чтобы получить список КонфигМапов, воспользуемся как обычно командой `kubectl get`: 

```bash
kubectl -n demo-cm get configmap
```

Как и для многих других ресурсов, используя kubectl команды, мы можем сокращать названия вида ресурса: 

```bash
kubectl -n demo-cm get cm
```

Если мы сделаем describe ресурса, то мы увидим его содержимое: 

```bash
kubectl -n demo-cm describe cm first-cm
```



### Монтируем ConfigMap к Поду

Создайте YAML-манифесты:

1) pod.yaml

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-with-cm
  namespace: demo-cm
spec:
  containers:
  - name: app
    image: busybox
    command: ["/bin/sh", "-c", "cat /etc/config/* && sleep 3600"]
    volumeMounts:
    - name: config-volume
      mountPath: /etc/config
  volumes:
  - name: config-volume
    configMap:
      name: first-cm
```

В спеке Пода описываются Вольюмы, здесь мы можем сделать Вольюм **config-volume** и затем монтировать его к Контейнерам, используя `volumeMounts`. То есть один Вольюм в Поде может быть примонтирован к нескольким Контейнерам этого Пода.

Задеплоим Под: 

```clojure
kubectl apply -f pod.yaml
```

Теперь, если мы посмотрим `kubectl describe` Пода, то мы увидим там Volume и Mount: 

```bash
kubectl -n demo-cm describe po pod-with-cm
```