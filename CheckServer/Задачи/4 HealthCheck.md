Условие:  
У тебя настроен Docker-контейнер с приложением и прописан HEALTHCHECK в Dockerfile:


HEALTHCHECK CMD curl --fail http://localhost:8080/health || exit 1


В CI всё работает. В Kubernetes — контейнер вечно “healthy”, но:
- Метрики не собираются  
- Ответы приложения не приходят  
- От клиентов жалобы  
- Никаких рестартов пода не происходит



При этом kubectl describe pod показывает статус: Ready: True, Containers: healthy

Задача:  
Найди источник проблемы, объясни, почему HEALTHCHECK ничего не выявляет, и предложи реальное решение.


Разбор:

🔍 Подвох №1: HEALTHCHECK в Docker — не работает в Kubernetes

- Kubernetes игнорирует HEALTHCHECK, прописанный в Dockerfile  
- Вместо этого использует livenessProbe и readinessProbe, заданные в манифесте YAML

📌 Поэтому контейнер кажется "здоровым", но на самом деле Kube о нём ничего не знает


🛠 Решение:

В deployment.yaml пропиши:


livenessProbe:
  httpGet:
    path: /health
    port: 8080
  initialDelaySeconds: 5
  periodSeconds: 10

readinessProbe:
  httpGet:
    path: /health
    port: 8080
  initialDelaySeconds: 2
  periodSeconds: 5


Это активирует реальную проверку на уровне Kubernetes и позволит поду:
- перезапускаться при зависании (liveness)  
- исключаться из сервисов (readiness)
