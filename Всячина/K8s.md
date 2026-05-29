Сделайте реализацию blue/green стратегии деплоймента для Kubernetes на основе деплойментов, сервиса и ingress’а и опишите как переключать версии.
### Конфигурация ingress'а 
apiVersion: networking.k8s.io/v1 
kind: Ingress
metadata: 
  name: app-ingress 
  annotations: 
    nginx.ingress.kubernetes.io/rewrite-target: / spec: 
      rules: 
      - http: 
          paths: 
          - path: / 
          pathType: Prefix 
          backend: 
            service: 
            name: app-service 
            port: 
              number: 80 

### Сервис 
apiVersion: v1 
kind: Service 
metadata: 
  name: app-service 
spec: 
  selector: 
    app: app 
    color: blue 
  ports: 
    - protocol: TCP 
    port: 80 
    targetPort: 8080 

### Деплоймент синей версии 
apiVersion: apps/v1 
kind: Deployment 
metadata: 
  name: app-blue 
  labels: 
    app: app 
    color: blue 
spec: 
  replicas: 1 
  selector: 
    matchLabels: 
      app: app 
      color: blue 
  template: 
    metadata: 
      labels: 
        app: app 
        color: blue 
    spec: 
      containers: 
      - name: app 
        image: docker.registry.io/app:1.0.0 
        ports: 
          - containerPort: 8080 

### Деплоймент зелёной версии 
apiVersion: apps/v1 
kind: Deployment 
metadata: 
  name: app-green
  labels: 
    app: app 
    color: green 
  spec: 
    replicas: 1 
    selector: 
      matchLabels: 
        app: app 
        color: green 
    template: 
      metadata: 
        labels: 
          app: app 
          color: green 
      spec: 
        containers: 
        - name: app 
          image: docker.registry.io/app:1.0.0 
          ports: 
            - containerPort: 8080 
### Переключение версий осуществляется путём изменения селекторов в сервисе
