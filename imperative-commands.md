# Imperative Commands with Kubectl

While you would be working mostly the declarative way - using definition files, imperative commands can help in getting one time tasks done quickly, as well as generate a definition template easily. This would help save considerable amount of time during your exams.

Before we begin, familiarize with the two options that can come in handy while working with the below commands:

`--dry-run`: By default as soon as the command is run, the resource will be created. If you simply want to test your command , use the `--dry-run` option. This will not create the resource, instead, tell you weather the resource can be created and if your command is right.

`-o yaml`: This will output the resource definition in YAML format on screen.

Use the above two in combination to generate a resource definition file quickly, that you can then modify and create resources as required, instead of creating the files from scratch.

## POD

Create an NGINX Pod

```bash
kubectl run --generator=run-pod/v1 nginx --image=nginx
```

Or the newer recommended way:

```bash
kubectl run nginx --restart=Never --image=nginx
```


Generate POD Manifest YAML file (`-o yaml`). Don't create it(`--dry-run`)

```bash
kubectl run --generator=run-pod/v1 nginx --image=nginx --dry-run -o yaml
```

## Deployment

Create a deployment

```bash
kubectl run --generator=deployment/v1beta1 nginx --image=nginx
```

Or the newer recommended way:

```bash
kubectl create deployment --image=nginx nginx
```

Generate Deployment YAML file (`-o yaml`). Don't create it(`--dry-run`)

```bash
kubectl run --generator=deployment/v1beta1 nginx --image=nginx --dry-run -o yaml
```

Or

```bash
kubectl create deployment --image=nginx nginx --dry-run -o yaml
```

Generate Deployment YAML file (`-o yaml`). Don't create it(`--dry-run`) with 4 Replicas (`--replicas=4`)

```bash
kubectl run --generator=deployment/v1beta1 nginx --image=nginx --dry-run --replicas=4 -o yaml
```

`kubectl create deployment` does not have a `--replicas` option. You could first create it and then scale it using the `kubectl scale` command.

Save it to a file - (If you need to modify or add some other details)

```bash
kubectl run --generator=deployment/v1beta1 nginx --image=nginx --dry-run --replicas=4 -o yaml > nginx-deployment.yaml
```

## Service

Create a Service named `redis-service` of type `ClusterIP` to expose pod `redis` on port `6379`

```bash
kubectl expose pod redis --port=6379 --name redis-service --dry-run -o yaml
```

(This will automatically use the pod's labels as selectors)

Or

`kubectl create service clusterip redis --tcp=6379:6379 --dry-run -o yaml`  (This will not use the pods labels as selectors, instead it will assume selectors as `app=redis`. You cannot pass in selectors as an option. So it **does not work very well if your pod has a different label set**. So generate the file and modify the selectors before creating the service)

Create a Service named `nginx-service` of type `NodePort` to expose pod nginx's port `80` on port `30080` on the nodes:

```bash
kubectl expose pod nginx --port=80 --name nginx-service --dry-run -o yaml
```

(This will automatically use the pod's labels as selectors, but **you cannot specify the node port**. You have to generate a definition file and then add the node port in manually before creating the service with the pod.)

Or

```bash
kubectl create service nodeport nginx --tcp=80:80 --node-port=30080 --dry-run -o yaml
```

(This will not use the pods labels as selectors)

Both the above commands have their own challenges. While one of it cannot accept a selector the other cannot accept a node port. I would recommend going with the `kubectl expose` command. If you need to specify a node port, generate a definition file using the same command and manually input the nodeport before creating the service.

## Reference

- [kubectl conventions](https://kubernetes.io/docs/reference/kubectl/conventions/)

### Create Deployment

Using `kubectl run`
`kubectl run` creates a deployment or job to manage the created container(s).

```bash
kubectl run -h

Usage:
  kubectl run NAME --image=image [--env="key=value"] [--port=port] [--replicas=replicas] [--dry-run=bool] [--overrides=inline-json] [--command] -- [COMMAND] [args...] [options]
```

Example:

```bash
kubectl run nginx --image=nginx
```

```bash
kubectl create -f deployment-definition.yaml
```

If you only want to create a pod with `kubectl run` then you must use a generator

```bash
kubectl run --generator=run-pod/v1 busybox --image=busybox:1.28 --command -- sleep 3600
```

### Get Deployment

```bash
kubectl get deployments
```

### Update Deployment

```bash
kubectl apply -f deployment-definition.yaml
```

```bash
kubectl edit deployment myapp-deployment
```

Using `kubectl set`

kubectl set image <deployment/name>:

```bash
kubectl set image deployment/myapp-deployment nginx=nginx:1.9.1
```

### Rollout Commands

kubectl rollout status <deployment/name>:

```bash
kubectl rollout status deployment/myapp-deployment
```

kubectl rollout history <deployment/name>:

```bash
kubectl rollout history deployment/myapp-deployment
```

#### Rollback a deployment

kubectl rollout undo <deployment/name>:

```bash
kubectl rollout undo deployment/myapp-deployment
```

### Create a ClusterRole.

```bash
kubectl create clusterrole         
```

### Create a ClusterRoleBinding for a particular ClusterRole

```bash
kubectl create clusterrolebinding  
```

### Create a configmap from a local file, directory or literal value

```bash
kubectl create configmap           
```

### Create a cronjob with the specified name.

```bash
kubectl create cronjob             
```

### Create a deployment with the specified name.

```bash
kubectl create deployment          
```

### Create a job with the specified name.

```bash
kubectl create job                 
```

### Create a namespace with the specified name

```bash
kubectl create namespace development
```

### Create a pod disruption budget with the specified name.

```bash
kubectl create poddisruptionbudget 
```

### Create a priorityclass with the specified name.

```bash
kubectl create priorityclass       
```

### Create a quota with the specified name.

```bash
kubectl create quota               
```

### Create a role with single rule.

```bash
kubectl create role developer --resource=pods --verb="create,list,get,update,delete" --namespace=development
```

### Create a RoleBinding for a particular Role or ClusterRole

```bash
kubectl create rolebinding developer-role-binding --role=developer --user=john --namespace=development
```

Verify role/rolebinding is working as expected

```bash
kubectl auth can-i create pods --namespace=development --as=john
yes

kubectl auth can-i list pods --namespace=development --as=john
yes

kubectl auth can-i get pods --namespace=development --as=john
yes

kubectl auth can-i update pods --namespace=development --as=john
yes

kubectl auth can-i delete pods --namespace=development --as=john
yes

kubectl auth can-i watch pods --namespace=development --as=john
no
```

### Create a secret using specified subcommand

```bash
kubectl create secret              
```

### Create a service using specified subcommand.

```bash
kubectl create service             
```

### Create a service account with the specified name

```bash
kubectl create serviceaccount      
```
