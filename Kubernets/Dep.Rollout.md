**Проверка истории деплоймента:**
```bash
kubectl -n rolling rollout history deployment/goapp-deployment
```

**Откат к предыдущей версии:**
```bash
kubectl -n rolling rollout undo deployment/goapp-deployment
```

**Плавный рестарт Подов:**

```bash
kubectl -n rolling rollout restart deployment/goapp-deployment
```