docker run -it --name=Container1 -v DataVolume1:/datavolume1 ubuntu
Создадим Container2 и смонтируйте том Container1:
docker run -it --name=Container2 --volumes-from Container1 ubuntu



Монтирование с привязкой (bind mount)
docker run -t -d -P -v /tmp/nginx/html:/usr/share/nginx/html --name nginxcont nginx:latest
t выделит псевдо-TTY для интерактивного взаимодействия с контейнером;
d запустит контейнер в фоновом режиме;
P привяжет контейнер к случайному порту;
v используем, чтобы в Docker примонтировать папку хоста в контейнер.

Узнаем IP-адрес.
docker container ls

