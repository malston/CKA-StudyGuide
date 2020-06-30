# DNS

## Create a deployment with a service exposing it over port 80

```bash
kubectl run busy-box-sleep --image="busybox:1.28" --expose=true --port=80 --namespace=development --labels="tier=web,env=dev" -- "sleep" "1000000"
```

One:

```bash
kubectl run ns-lookup --image="busybox:1.28" --restart=Never --rm -it -- nslookup busy-box-sleep.development.svc > svc.txt
kubectl run ns-lookup --image="busybox:1.28" --restart=Never --rm -it -- nslookup 172-24-3-2.development.pod > pod.txt
```

Alternatively:

```bash
kubectl run ns-lookup --image="busybox:1.28" --restart=Never -- sleep 10000000
kubectl exec ns-lookup -it -- nslookup busy-box-sleep.development.svc > svc.txt
kubectl exec ns-lookup -it -- nslookup 172-24-3-2.development.pod > pod.txt
```
