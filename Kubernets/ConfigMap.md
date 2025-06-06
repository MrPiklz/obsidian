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


### Монтирование файлов

БД.

```yaml
## пример config.yaml

app:
  name: my-application
  version: 1.0.0

server:
  port: 8080
  host: 0.0.0.0

database:
  host: localhost
  port: 5432
  user: db_user
  password: db_password
  name: my_database

logging:
  level: info
```

1. deployment.yaml
    
прописываем к деплоймент монтирование конфига
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: deployment-with-cm
  namespace: demo-cm
spec:
  replicas: 2
  selector:
    matchLabels:
      app: demo-app
  template:
    metadata:
      labels:
        app: demo-app
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


Императивный подход

Допустим, Вы создали TLS ключ: 

```bash
openssl genrsa -out server.key 2048
```

И Вам надо превратить его в ConfigMap, чтобы использовать в приложении. Для этого Вы можете создать ConfigMap, используя команду: 

```bash
kubectl -n demo-cm create configmap test-tls-key --from-file="server.key"
```

Но если Вы всё-таки ищете простой способ создать подобный ConfigMap, но при этом хотите соблюдать подход IaC, то вы можете сделать dry-run этой команды, получить и сохранить ямлик, чтобы потом применить его через `kubectl apply`:

```bash
kubectl -n demo-cm create configmap test-tls-key --from-file="server.key" --dry-run="client" -o yaml
```

### Переменные окружения


1. cm-env.yaml
    

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: env-config
  namespace: demo-cm
data:
  LOG_LEVEL: "debug"
  DATABASE_URL: "postgresql://user:password@db:5432/mydatabase"
```

      2. deployment-env.yaml  

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: deployment-with-env
  namespace: demo-cm
spec:
  replicas: 1
  selector:
    matchLabels:
      app: demo-app
  template:
    metadata:
      labels:
        app: demo-app
    spec:
      containers:
      - name: app
        image: busybox
        command: ["/bin/sh", "-c", "echo \"Log Level: $LOG_LEVEL, Database URL: $DATABASE_URL\" && sleep 3600"]
        envFrom:
        - configMapRef:
            name: env-config
```

Здесь мы уже не описываем конструкцию Volume, вместо этого мы задаем поле `envFrom`. В этом поле мы делаем референс на ConfigMap, из которого нужно взять переменные окружения.

      3. Примените манифесты: 

```bash
kubectl apply -f cm-env.yaml 
kubectl apply -f deployment-env.yaml
```