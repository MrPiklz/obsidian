**Проверка истории деплоймента:**
```bash
kubectl -n rolling rollout history deployment/goapp-deployment
```

**Откат к предыдущей версии:**
```bash
kubectl -n rolling rollout undo deployment/goapp-deployment --to-revision 1 .name/app rolled back
```

**Плавный рестарт Подов:**

```bash
kubectl -n rolling rollout restart deployment/goapp-deployment
```