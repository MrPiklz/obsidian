
https://kubernetes.io/docs/concepts/security/pod-security-policy/
https://github.com/open-policy-agent/gatekeeper
https://twitter.com/mauilion/status/1129468485480751104   ###
https://github.com/kvaps/kubectl-node-shell
## Проблемные поды:
#### Поды
_Проблемный под 1: все разрешено_
_Проблемный под 2: Privileged и hostPid_
_Проблемный под 3: только Privileged_
_Проблемный под 4: только hostPath_
_Проблемный под 5: только hostPid_
_Проблемный под 6: только hostNetwork_
_Проблемный под 7: только hostIPC_
_Проблемный под 8: все запрещено._

k1. Выполнение команды в уже запущенном поде kubectl exec --stdin --tty nginx-demo -- /bin/bash
k2. Поднять свой под

deployment или daemonset, содержащий их зараженный контейнер, в первом случае будет равномерно запущено какое‑то количество подов, во втором — поды будут на каждой ноде

kubectl cronjob
kubectl get jobs



- Network Policy – сетевые политики смогли бы ограничить общение контейнеров по сети. Например можно написать такую политику, которая запретит стучаться из Pod с веб-приложением в Pod с Grafana
- Policy engine – имея правило на запрет создания привилегированных Pod, от Kyverno или Gatekeeper, у злоумышленника и вовсе не получилось бы сбежать на хост. Запрос не прошел бы через webhook
- Runtime observability & security – знание и полная видимость происходящего в кластере позволили бы заметить и остановить злоумышленника ещё в начале атаки