Оптимизация контейнеров как у SRE Google

Контейнер — это не просто Dockerfile. Это микросистема, и если её не настраивать — она будет жечь CPU, RAM и SSD без пользы. Вот  🔥 продвинутые советы по настройке контейнеров:

⚫️  Ограничь права контейнера

docker run --read-only --cap-drop=ALL --security-opt no-new-privileges ...

▪️ --read-only — защищает файловую систему  
▪️ --cap-drop=ALL — удаляет лишние привилегии  
▪️ no-new-privileges — запрещает повышение прав в процессе

⚫️  Установи лимиты CPU и памяти

docker run --memory="512m" --cpus="1.5" ...

▪️ Не давай контейнеру жрать весь хост — особенно на multi-tenant нодах  
▪️ Используй cpu-shares, cpuset, ulimits для тонкой настройки

⚫️  Чисти от мусора
- Используй multi-stage builds — минимизируй размер образа  
- Оставляй только необходимые бинарники и конфиги  
- Пример:

FROM golang:1.22 as builder
WORKDIR /app
COPY . .
RUN go build -o app

FROM alpine:3.19
COPY --from=builder /app/app /bin/app
ENTRYPOINT ["/bin/app"]


⚫️  Используй distroless или Alpine
- Образы типа gcr.io/distroless/static — без шелла, package manager и мусора  
- alpine — легче, но следи за совместимостью с glibc

⚫️  Пропиши healthcheck

HEALTHCHECK --interval=30s --timeout=3s CMD curl -f http://localhost:8080/health || exit 1

▪️ Kubernetes будет перезапускать только при реальных сбоях

⚫️ Подключи observability
- Встраивай Prometheus exporter  
- Логи — в stdout/stderr (для kubectl logs и EFK/PLG стека)  
- Используй otel, если нужен tracing

⚫️  Проверь, чем занят контейнер

docker top <container>
docker inspect --format '{{ .HostConfig }}' <container>

▪️ Вовремя заметишь, если процесс форкает что-то лишнее или идёт через /dev

💡 Эти практики критичны, если:
- Вы деплоите в прод с autoscaling  
- Контейнеры крутятся в k8s или Fargate  
- Вам важно сократить издержки на ресурсы и повысить безопасность