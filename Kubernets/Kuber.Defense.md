Токен сервис аккаунта
/var/run/secrets/kubernetes.io/serviceaccount/token

сервис аккаунт доступ не отключен
/api/v1/namespaces/default/endpoints



имя токена всегда содержит название Namespace и сервис-аккаунт
k get podsv -n
тут отдаст system:serviceaccount:cows:cow  
                             namespace:service
посмотреть поды
k get pods -n cows

посмотреть права
k auth can-i --list
в нашем случае нет прав в Default Namespace, но в Namespace Cowns они практически не ограничены
k auth can-i --list -n cowns

Секция Security Context в манифестах отвечает за настройки безопасности. Она позволяет задействовать разные варианты защиты, в том числе использовать:  
  - runAsNotRoot,  
   - runAsUser/runAsGroup,  
    - Privileged,  
    - Capabilities,  
    - readOnlyRootFileSystem,  
    - procMount,  
    - Sysctls и другие.
-В данном случае большой выбор часто играет на руку злоумышленникам. При подготовке Kubernetes разработчики часто забывают о некоторых флажках и оставляют пробелы в защите. Например, контейнер может оказаться Rootless
sudo su -
возможность запись
touch test

Далее можно попробовать запустить под благодаря правам с Root внутри. Для этого подойдет Alpine, в нем это легко сделать, поскольку есть Root по умолчанию.

  

- использование образов только из доверенных registry;  
    
- запрет на использование тега Latest в образах;  
    
- обязательное использование limits и requests;  
    
- запуск привилегированных контейнеров;  
    
- запрет на использование hostPID и hostIPC

hostfilesystem. Это политика Gatekeeper, которая позволяет указывать список директорий, доступных для монтирования, а также выставлять для них права на чтение.


- Network Policy – сетевые политики смогли бы ограничить общение контейнеров по сети. Например можно написать такую политику, которая запретит стучаться из Pod с веб-приложением в Pod с Grafana
- Policy engine – имея правило на запрет создания привилегированных Pod, от Kyverno или Gatekeeper, у злоумышленника и вовсе не получилось бы сбежать на хост. Запрос не прошел бы через webhook
- Runtime observability & security – знание и полная видимость происходящего в кластере позволили бы заметить и остановить злоумышленника ещё в начале атаки