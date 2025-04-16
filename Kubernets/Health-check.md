**livenessProbe**

liveness - проба позволяет Kubernetes делать Health-check'и и при непрохождении определенного в описании Пробы количества проверок Kubernetes перезагрузит контейнер в Поде.

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    test: my-pod
  name: my-pod-http
spec:
  containers:
  - name: containername
    image: k8s.gcr.io/liveness
    args:
    - /server
    livenessProbe:
      httpGet:
        path: /healthz
        port: 8080
      initialDelaySeconds: 3
      periodSeconds: 2
```

- `initialDelaySeconds`: 3 - означает, что после запуска Пода и перед первой Проверкой пройдет 3 секунды. По умолчанию - 0 секунд.
- `periodSeconds`: 2 - означает, что проверки будут происходить каждый 2 секунды. По умолчанию - 10 секунд.
- `failureThreshold`:  количество повторных Проверок перед рестартом. По умолчанию 3
- `timeoutSeconds`: Количество секунд ожидания ответа на Проверке. По умолчанию 1 секунда. 
- `successThreshold`: Минимальное количество последовательных проверок, чтобы проба считалась успешной после неудачной. По умолчанию 1.


**readinessProbe**

Допустим у Вас деплоймент с 3 репликами Подов, на один из Подов попал крупный запрос и он ушел 20 секунд его отрабатывать, лучшим решением будет перенаправлять трафик на другие Поды, пока загруженный не отработает. Так работает readinessProbe, в случае ее непрохождение, Kubernetes Service не будет посылать запросы на Под.

```yaml
readinessProbe:
  exec:
    command:
    - cat
    - /tmp/healthy
  initialDelaySeconds: 5
  periodSeconds: 5
```


**startupProbe**

Мы периодически сталкиваемся с кейсами, когда приложение стартует довольно долго. Например, мы дали ему мало CPU (так как в покое ему много и не надо), и оно запускается 5 минут, в результате чего не проходит все readiness/liveness проверки и попадает в вечный ребут (CrashLoop). _startupProbe_  решает эту проблему, ведь если она настроена, liveness и readiness Пробы не будут отрабатывать, пока не завершится работа Стартап Пробы.

```yaml
startupProbe:
  httpGet:
    path: /healthz
    port: liveness-port
  failureThreshold: 30
  periodSeconds: 10
```