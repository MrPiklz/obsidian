

###NON ROOT

```
FROM alpine:3.12
# Create user and set ownership and permissions as required
RUN adduser -D myuser && chown -R myuser /myapp-data
# ... copy application files
USER myuser
ENV APP_TMP_DATA=/tmp
ENTRYPOINT ["/myapp"]
```

## Не делайте привязку к определенному UID

### Назначить root владельцем исполняемых файлов и запретить изменять эти файлы
Также исполняемые файлы не должны быть доступны на запись всем пользователям.

### Многоступенчатые сборки
Пример многоступенчатой сборки для приложения на go:

```
#This is the "builder" stage
FROM golang:1.15 as builder
WORKDIR /my-go-app
COPY app-src .
RUN GOOS=linux GOARCH=amd64 go build ./cmd/app-service
#This is the final stage, and we copy artifacts from "builder"
FROM gcr.io/distroless/static-debian10
COPY --from=builder /my-go-app/app-service /bin/app-service
ENTRYPOINT ["/bin/app-service"]
```

### Distroless, from scratch
Использование минимального базового образа

### Используйте проверенные базовые образы
### Своевременно обновляйте образы

### Открытые порты
Каждый открытый порт в вашем контейнере - это открытая дверь в вашу систему.
Используйте команду EXPOSE в Dockerfile только чтобы обозначить и задокументировать необходимые порты

### Учетные данные и конфиденциальность
Никогда не помещайте чувствительные данные или учетные данные в Dockerfile

### ADD, COPY
Инструкции ADD и COPY предоставляют аналогичные функции в файле Dockerfile. Однако использование COPY является предпочтительным.


### Контекст сборки и dockerignore
В соответствии с Dockerfile best practice нужно создать подпапку, содержащую файлы, которые необходимо скопировать внутри контейнера, использовать ее в качестве контекста сборки и, когда это возможно, явно указывать инструкции COPY (избегайте подстановочных знаков). Например:

```
docker build -t myimage files/
```

### Порядок слоев
Поскольку RUN, COPY, ADD и другие инструкции создают новый слой контейнера, группировка нескольких команд вместе уменьшит количество слоев.
Кроме того, сначала разместите команды, которые с меньшей вероятностью будут изменены и которые легче кэшировать.
Например, вместо:

```
FROM ubuntu
RUN apt-get install -y wget
RUN wget https://…/downloadedfile.tar
RUN tar xvzf downloadedfile.tar
RUN rm downloadedfile.tar
RUN apt-get remove wget
```

```
FROM ubuntu
RUN apt-get install wget && wget https://…/downloadedfile.tar && tar xvzf downloadedfile.tar && rm downloadedfile.tar && apt-get remove wget
```

### Metadata labels
Рекомендуется включать Dockerfile метки метаданных при создании образа.

###  Тестируйте ваши Dockerfile
Такие инструменты, как Haskell Dockerfile Linter (hadolint), могут обнаруживать ошибки в вашем Dockerfile и даже обнаружить проблемы внутри таких команд как RUN.
Сканеры образов также способны обнаруживать уязвимости

### Локальное сканирование образов

### Docker port socket and TCP protection
Докер-сокет - это большая привилегированная дверь в вашу хост-систему, которая, как недавно было замечено, может использоваться для вторжений и использования вредоносного программного обеспечения. Убедитесь, что ваш /var/run/docker.sock имеет правильные разрешения, и если докер доступен через TCP (что вообще не рекомендуется), убедитесь, что он должным образом защищен.

### Цифровая подпись образов
Использование Docker Content Trust, Docker notary, Harbour notary или аналогичные инструменты для цифровой подписи ваших образов и последующей проверки их во время выполнения - одна из лучших практик Dockerfile.

Включение проверки подписи отличается в каждой среде выполнения. Например, в докере это делается с помощью переменной окружения DOCKER_CONTENT_TRUST: экспорт DOCKER_CONTENT_TRUST = 1

### Изменение тэгов

### Запуск как non-root

### Включить проверки
При использовании Docker или Docker Swarm по возможности включайте инструкцию HEALTHCHECK в свой Dockerfile.