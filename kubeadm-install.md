# Creating a cluster with the kubeadm tool

## Following the kubernetes.io docs for installing kubeadm

Check to see what version of OS you're running

```bash
cat /etc/os-release
```

```bash
sudo apt-get update && sudo apt-get install -y apt-transport-https curl

curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
cat <<EOF | sudo tee /etc/apt/sources.list.d/kubernetes.list
deb https://apt.kubernetes.io/ kubernetes-xenial main
EOF

sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```

```bash
kubeadm version -o short
kubelet --version
```

```bash
export LOADBALANCER_ADDRESS=192.168.5.30
export LOADBALANCER_PORT=6443
cat <<EOF | sudo tee kubeadm-config.yaml
apiVersion: kubeadm.k8s.io/v1beta2
kind: ClusterConfiguration
kubernetesVersion: stable
controlPlaneEndpoint: "${LOADBALANCER_ADDRESS}:${LOADBALANCER_PORT}"
EOF
```

```bash
sudo kubeadm init --config=kubeadm-config.yaml \
    --upload-certs
```

```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

```bash
kubectl get nodes
```

```bash
ssh node01
```

```bash
sudo kubeadm join 172.17.0.47:6443 --token g21pve.f0v9wjcx7saf70mm \
    --discovery-token-ca-cert-hash sha256:d0e9c586b7f46812caeb6a5e9109ef53d73f2d96a7c4ac260f71e6a4b64a8f4f

```bash
scp root@master:/etc/kubernetes/admin.conf .
kubectl --kubeconfig ./admin.conf get nodes
```

OR

```bash
node01 $ exit
logout
Connection to node01 closed.
master $ kubectl get nodes
NAME     STATUS     ROLES    AGE     VERSION
master   NotReady   master   7m17s   v1.17.0
node01   NotReady   <none>   93s     v1.17.0
master $
```

## Install Weave Net pod network add-on

Set `/proc/sys/net/bridge/bridge-nf-call-iptables` to `1` by running `sysctl net.bridge.bridge-nf-call-iptables=1` to pass bridged IPv4 traffic to iptables' chains. This is a requirement for some CNI plugins to work, for more information please see [here](https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/network-plugins/#network-plugin-requirements).

```bash
master $ sysctl net.bridge.bridge-nf-call-iptables=1
master $ ssh node01

node01 $ sysctl net.bridge.bridge-nf-call-iptables=1
node01 $ exit
```

```bash
master $ kubectl apply -f "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')"
serviceaccount/weave-net created
clusterrole.rbac.authorization.k8s.io/weave-net created
clusterrolebinding.rbac.authorization.k8s.io/weave-net created
role.rbac.authorization.k8s.io/weave-net created
rolebinding.rbac.authorization.k8s.io/weave-net created
daemonset.apps/weave-net created
```

```bash
master $ kubectl get nodes
NAME     STATUS     ROLES    AGE     VERSION
master   NotReady   master   11m     v1.17.0
node01   NotReady   <none>   5m47s   v1.17.0
```

```bash
master $ kubectl get pods --all-namespaces
NAMESPACE     NAME                             READY   STATUS    RESTARTS   AGE
kube-system   coredns-6955765f44-9fspc         1/1     Running   0          12m
kube-system   coredns-6955765f44-l7kqn         1/1     Running   0          12m
kube-system   etcd-master                      1/1     Running   0          11m
kube-system   kube-apiserver-master            1/1     Running   0          11m
kube-system   kube-controller-manager-master   1/1     Running   0          11m
kube-system   kube-proxy-lq4v9                 1/1     Running   0          12m
kube-system   kube-proxy-m9jxk                 1/1     Running   0          6m29s
kube-system   kube-scheduler-master            1/1     Running   0          11m
kube-system   weave-net-4xpmv                  2/2     Running   0          53s
kube-system   weave-net-m9w9b                  2/2     Running   0          53s
```

```bash
master $ kubectl get nodes
NAME     STATUS   ROLES    AGE     VERSION
master   Ready    master   12m     v1.17.0
node01   Ready    <none>   7m11s   v1.17.0
master $
```