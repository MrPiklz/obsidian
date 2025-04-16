#### **Ingress Controller**

Для работы Ingress в Kubernetes требуется **Ingress Controller** — специальный контроллер, который управляет трафиком и реализует правила маршрутизации, описанные в Ingress ресурсах.

Ingress ресурс — это вид ресурса в Kubernetes (`kind: Ingress`), в котором описываются правила маршрутизации запросов. Например, вы можете указать, что запросы на `example.com/app1` должны перенаправляться на один Service, а запросы на `example.com/app2` — на другой.



Теперь настроим Ingress ресурс. В папке `ingress` опишем следующие манифесты: 

1. 01_ns.yaml
    
    ```yaml
    apiVersion: v1
    kind: Namespace
    metadata:
      name: demo-ingress
    ```
    
2. 02_deployment.yaml
    
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
    ```
    
3. 03_service.yaml
    
    ```yaml
    apiVersion: v1
    kind: Service
    metadata:
      namespace: demo-ingress
      name: goweb-svc
    spec:
      selector:
        app: goapp
      ports:
        - protocol: TCP
          port: 80
          targetPort: 8080
    ```
    
4. 04_ingress.yaml
    
    ```bash
    apiVersion: networking.k8s.io/v1
    kind: Ingress
    metadata:
      name: example-ingress
      namespace: demo-ingress
    spec:
      ingressClassName: nginx
      rules:
        - host: hello-world.example
          http:
            paths:
              - path: /
                pathType: Prefix
                backend:
                  service:
                    name: goweb-svc
                    port:
                      number: 80
    ```
    
5. Получите адрес Ноды Миникуба командой `minikube ip` и добавьте запись в файл hosts: 
    
    ```undefined
    192.168.49.2	hello-world.example
    ```
    
6. Задеплоим все ресурсы: 
    
    ```bash
    kubectl apply -f ingress/
    ```
    
7. Проверим, что все ресурсы создались: 
    
    ```bash
    $ kubectl -n demo-ingress get po
    NAME                                READY   STATUS    RESTARTS        AGE
    goapp-deployment-86596c5548-qnbl6   1/1     Running   1 (5m37s ago)   43h
    
    $ kubectl -n demo-ingress get svc
    NAME        TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)   AGE
    goweb-svc   ClusterIP   10.103.252.192   <none>        80/TCP    43h
    
    $ kubectl -n demo-ingress get ingress
    NAME              CLASS   HOSTS                 ADDRESS        PORTS   AGE
    example-ingress   nginx   hello-world.example   192.168.49.2   80      43h
    ```
    
8. Дождитесь, пока у Ingress ресурса появится IP-адрес и проверьте функционал: 
    
    ```ruby
    $ curl http://hello-world.example/ingress
    
    Hi there, I love ingress!
    ```