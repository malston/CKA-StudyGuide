Test the connectivity to the service

```bash
master $ kubectl run test-np --image=busybox:1.28 --restart=Never --rm -it -- sh

/ nc -z -v -w 2 np-test-service 80
nc: np-test-service (10.98.213.202:80): Connection timed out

/ #
```

```
kubectl get netpol

kubectl describe netpol default-deny


kubectl api-resources | grep netpol

kubectl api-resources --api-group networking.k8s.io -o wide

kubectl api-versions | grep networking.k8s.io

