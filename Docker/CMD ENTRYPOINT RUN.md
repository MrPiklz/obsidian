RUN/CMD/ENTRYPOINT
при сборке образа/при старте контейнера/

`RUN`, `CMD` и `ENTRYPOINT`. Эти способы называются **shell form** и **exec form**.

![[6c8bfccfbedcf0e1032323327cb18b90.png]]
![[80fdd92170aaae4ea46a921d648695c4.png]]

Форма shell необходима для команд, требующих функциональности оболочки, таких как пайплайны или глобализация файлов. Однако форма exec предпочтительнее для простых команд без функций оболочки, поскольку она снижает сложность и вероятность ошибок.

```
# Shell form, useful for complex scriptingRUN apt-get update && apt-get install -y nginx 
# Exec form, for direct command executionRUN ["apt-get", "update"]RUN ["apt-get", "install", "-y", "nginx"]
```

```
# ENTRYPOINT with exec form for direct process control
ENTRYPOINT ["httpd"] 
# CMD provides default parameters, can be overridden at runtime
CMD ["-D", "FOREGROUND"]
```

![[92518edbe0329ed7a929efae344c799a.png]]



![[9409acd5570c1d58ac00326ca16a32fa.png]]


![[d2aea848c2668143110a368849577489.png]]
