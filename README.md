
### on all node:
```bash
cd /tmp
git clone https://github.com/levancao798/deploy-k8s-cluster.git
```
###### set static ip:
```bash
vim /tmp/deploy-k8s-cluster/netplan-config.yaml
cat /tmp/deploy-k8s-cluster/netplan-config.yaml > /etc/netplan/00-installer-config.yaml
netplan apply
```
###### add ssh pubkey:
```bash
cat ~/.ssh/id_rsa.pub |ssh user@ip 'cat >> ~/.ssh/authorized_keys'
```
###### install dockerce, kubenetes:
```bash
/bin/bash /tmp/deploy-k8s-cluster/setup-node.sh
```
### on master node (edit kubeadm-master-init.yaml file):
```bash
vim /tmp/deploy-k8s-cluster/kubeadm-master-init.yaml
mv /tmp/deploy-k8s-cluster/kubeadm-master-init.yaml /etc/kubernetes/kubeadm-master-init.yaml 
kubeadm init --config /etc/kubernetes/kubeadm-master-init.yaml --upload-certs
```
###### coppy from master to worker:
```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
kubectl -n kube-public get configmap cluster-info -o jsonpath='{.data.kubeconfig}' > discovery.yaml 
scp discovery.yaml user@host:/etc/kubernetes
```
###### install CNI:
```bash
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
```
### on worker (edit kubeadm-worker-init.yaml file):
```bash
vim /tmp/deploy-k8s-cluster/kubeadm-worker-init.yaml
mv /tmp/deploy-k8s-cluster/kubeadm-worker-init.yaml /etc/kubernetes/kubeadm-worker-init.yaml 
kubeadm join --config /etc/kubernetes/kubeadm-worker-init.yaml
```
### install k9s on ubuntu
```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
brew install k9s
vim $HOME/.bashrc
export PATH=/home/linuxbrew/.linuxbrew/bin:$PATH
```
