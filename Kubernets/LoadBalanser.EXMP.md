1. ns.yaml
    
    ```yaml
    apiVersion: v1
    kind: Namespace
    metadata:
      name: demo-lb
    ```
    
2. deployment.yaml
    
    ```yaml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: goapp-deployment
      namespace: demo-lb
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
    
3. lb.yaml
    
    ```yaml
    apiVersion: v1
    kind: Service
    metadata:
      namespace: demo-lb
      name: goweb-svc-lb
    spec:
      type: LoadBalancer
      selector:
        app: goapp
      ports:
        - protocol: TCP
          port: 80
          targetPort: 8080
    ```
    

```delphi
$ kubectl -n demo-lb get svc

NAME           TYPE           CLUSTER-IP   EXTERNAL-IP    PORT(S)        AGE
goweb-svc-lb   LoadBalancer   10.43.84.7   192.168.10.1   80:30742/TCP   10s
```

`EXTERNAL-IP` - это адрес, доступный извне кластера, который назначился Сервису. Используя такой сервис, мы и сами можем задавать адреса, указывая их в `spec.loadBalancerI`