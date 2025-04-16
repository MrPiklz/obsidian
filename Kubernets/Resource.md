
### Requests 

Реквесты определяют минимальное количество ресурсов (CPU и памяти), которые Kubernetes выделяет вашему Контейнеру в Поде. Это ресурсы, которые Под гарантированно получит.

```yaml
resources:
  requests:
    memory: "100M"
    cpu: "200m"
```

Лимиты — это максимальный объем ресурсов, который Контейнер в Поде может использовать. Если приложение потребует больше ресурсов, чем указано в лимитах, Kubernetes не даст ему превысить эти ограничения.

- Если приложение потребляет больше CPU, чем указано в лимитах, оно начнет работать медленнее, включается CPU Throttling
- При превышении лимита по памяти — процесс, а стало быть и Контейнер в Поде будет завершен OOM Killer'ом. Сам Под при этом пересоздаваться не будет

```yaml
resources:
  limits:
    memory: "150Mi"
    cpu: "300m"
```


### ример

Манифест Деплоймента с заданными`requests` и `limits`

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: goapp-deployment
  namespace: demo-ingress
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
        resources:
          requests:
            memory: "100Mi"
            cpu: "200m"
          limits:
            memory: "150Mi"
            cpu: "300m"
```


В зависимости от того, как Вы задаете requests и limits, Kubernetes присваивает Поду один из трех классов **QoS** (Quality of Service):

1. **Guaranteed** — если requests и limits равны, Под всегда получит ровно столько ресурсов, сколько указано.
2. **Burstable** — если requests ниже limits, Под гарантированно получит реквесты, но может использовать больше ресурсов, если они доступны.
3. **Best Effort** — если ни requests, ни limits не указаны, Под может использовать ресурсы только если они доступны. У таких Подов самый низкий приоритет.