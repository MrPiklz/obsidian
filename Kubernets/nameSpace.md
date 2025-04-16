```
apiVersion: v1 
kind: Namespace 
metadata: 
	name: examples-dev
```

  ```
  kubectl apply -f ns.yaml
  ```

```bash
kubectl get ns  #создать нэймспэйс
```

```bash
kubectl delete -f ns.yaml  #удалить
```


императивно

```bash
    kubectl create ns examples-dev2
    ```
kubectl get ns
kubectl delete ns examples-dev2