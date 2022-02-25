# Kubernetes CKS Course Environment

This is the repository of the [CKS FULL COURSE](https://killer.sh/r?d=cks-course)

There is also just the [CKS SIMULATOR](https://killer.sh/cks)

And the [CKS CHALLENGE SERIES](https://killer.sh/r?d=cks-series)


## Setup Cluster in GCP

## CKS MASTER NODE

#### Create VM
```
1. create VM:
name: cks-master
family: e2-medium (2vCPU, 4GB)
image: ubuntu18.04 LTS bionic
disk: 50GB
```

Like:
```
gcloud compute instances create cks-master --zone=europe-west2-c \
--machine-type=e2-medium \
--image=ubuntu-1804-bionic-v20201014 \
--image-project=ubuntu-os-cloud \
--boot-disk-size=50GB
```

```
gcloud compute ssh cks-master
```

#### Configure
```
sudo -i
bash <(curl -s https://raw.githubusercontent.com/killer-sh/cks-course-environment/master/cluster-setup/latest/install_master.sh)
```

```
gcloud compute ssh cks-worker
vi master.sh
```
<details>
<summary>[Click to expand] Copy this script into master.sh and run it </summary>

```
#!/bin/sh

# Source: http://kubernetes.io/docs/getting-started-guides/kubeadm

set -e

KUBE_VERSION=1.22.2


### setup terminal
apt-get update
apt-get install -y bash-completion binutils
echo 'colorscheme ron' >> ~/.vimrc
echo 'set tabstop=2' >> ~/.vimrc
echo 'set shiftwidth=2' >> ~/.vimrc
echo 'set expandtab' >> ~/.vimrc
echo 'source <(kubectl completion bash)' >> ~/.bashrc
echo 'alias k=kubectl' >> ~/.bashrc
echo 'alias c=clear' >> ~/.bashrc
echo 'complete -F __start_kubectl k' >> ~/.bashrc
sed -i '1s/^/force_color_prompt=yes\n/' ~/.bashrc


### disable linux swap and remove any existing swap partitions
swapoff -a
sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab


### remove packages
kubeadm reset -f || true
crictl rm --force $(crictl ps -a -q) || true
apt-mark unhold kubelet kubeadm kubectl kubernetes-cni || true
apt-get remove -y docker.io containerd kubelet kubeadm kubectl kubernetes-cni || true
apt-get autoremove -y
systemctl daemon-reload


### install packages
curl https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
cat <<EOF > /etc/apt/sources.list.d/kubernetes.list
deb http://apt.kubernetes.io/ kubernetes-xenial main
EOF
apt-get update
apt-get install -y docker.io containerd kubelet=${KUBE_VERSION}-00 kubeadm=${KUBE_VERSION}-00 kubectl=${KUBE_VERSION}-00 kubernetes-cni
apt-mark hold kubelet kubeadm kubectl kubernetes-cni


### containerd
cat <<EOF | sudo tee /etc/modules-load.d/containerd.conf
overlay
br_netfilter
EOF
sudo modprobe overlay
sudo modprobe br_netfilter
cat <<EOF | sudo tee /etc/sysctl.d/99-kubernetes-cri.conf
net.bridge.bridge-nf-call-iptables  = 1
net.ipv4.ip_forward                 = 1
net.bridge.bridge-nf-call-ip6tables = 1
EOF
sudo sysctl --system
sudo mkdir -p /etc/containerd


### containerd config
cat > /etc/containerd/config.toml <<EOF
disabled_plugins = []
imports = []
oom_score = 0
plugin_dir = ""
required_plugins = []
root = "/var/lib/containerd"
state = "/run/containerd"
version = 2

[plugins]

  [plugins."io.containerd.grpc.v1.cri".containerd.runtimes]
    [plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc]
      base_runtime_spec = ""
      container_annotations = []
      pod_annotations = []
      privileged_without_host_devices = false
      runtime_engine = ""
      runtime_root = ""
      runtime_type = "io.containerd.runc.v2"

      [plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc.options]
        BinaryName = ""
        CriuImagePath = ""
        CriuPath = ""
        CriuWorkPath = ""
        IoGid = 0
        IoUid = 0
        NoNewKeyring = false
        NoPivotRoot = false
        Root = ""
        ShimCgroup = ""
        SystemdCgroup = true
EOF


### crictl uses containerd as default
{
cat <<EOF | sudo tee /etc/crictl.yaml
runtime-endpoint: unix:///run/containerd/containerd.sock
EOF
}


### kubelet should use containerd
{
cat <<EOF | sudo tee /etc/default/kubelet
KUBELET_EXTRA_ARGS="--container-runtime remote --container-runtime-endpoint unix:///run/containerd/containerd.sock"
EOF
}


### install podman
apt-get install software-properties-common -y
add-apt-repository -y ppa:projectatomic/ppa
sudo apt-get -qq -y install podman containers-common
cat <<EOF | sudo tee /etc/containers/registries.conf
[registries.search]
registries = ['docker.io']
EOF


### start services
systemctl daemon-reload
systemctl enable containerd
systemctl restart containerd
systemctl enable kubelet && systemctl start kubelet


### init k8s
rm /root/.kube/config || true
kubeadm init --kubernetes-version=${KUBE_VERSION} --ignore-preflight-errors=NumCPU --skip-token-print

mkdir -p ~/.kube
sudo cp -i /etc/kubernetes/admin.conf ~/.kube/config

# workaround because https://github.com/weaveworks/weave/issues/3927
# kubectl apply -f "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')"
curl -L https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n') -o weave.yaml
sed -i 's/ghcr.io\/weaveworks\/launcher/docker.io\/weaveworks/g' weave.yaml
kubectl -f weave.yaml apply
rm weave.yaml

apt-mark unhold kubelet kubeadm kubectl kubernetes-cni


# etcdctl
ETCDCTL_VERSION=v3.5.1
ETCDCTL_VERSION_FULL=etcd-${ETCDCTL_VERSION}-linux-amd64
wget https://github.com/etcd-io/etcd/releases/download/${ETCDCTL_VERSION}/${ETCDCTL_VERSION_FULL}.tar.gz
tar xzf ${ETCDCTL_VERSION_FULL}.tar.gz
mv ${ETCDCTL_VERSION_FULL}/etcdctl /usr/bin/
rm -rf ${ETCDCTL_VERSION_FULL} ${ETCDCTL_VERSION_FULL}.tar.gz

echo
echo "### COMMAND TO ADD A WORKER NODE ###"
kubeadm token create --print-join-command --ttl 0

```
</details>

-------------------

## CKS WORKER NODE

#### Create VM
```
1. create VM:
name: cks-worker
family: e2-medium (2vCPU, 4GB)
image: ubuntu18.04 LTS bionic
disk: 50GB
```

Like:
```
gcloud compute instances create cks-worker --zone=europe-west2-c \
--machine-type=e2-medium \
--image=ubuntu-1804-bionic-v20201014 \
--image-project=ubuntu-os-cloud \
--boot-disk-size=50GB
```

```
gcloud compute ssh cks-worker
```

#### Configure
```
sudo -i
bash <(curl -s https://raw.githubusercontent.com/killer-sh/cks-course-environment/master/cluster-setup/latest/install_worker.sh)
```

```
vi worker.sh
```
<details>
<summary>[Click to expand] Copy this script into worker.sh and run it </summary>

```
#!/bin/sh

# Source: http://kubernetes.io/docs/getting-started-guides/kubeadm

set -e

KUBE_VERSION=1.22.2


### setup terminal
apt-get update
apt-get install -y bash-completion binutils
echo 'colorscheme ron' >> ~/.vimrc
echo 'set tabstop=2' >> ~/.vimrc
echo 'set shiftwidth=2' >> ~/.vimrc
echo 'set expandtab' >> ~/.vimrc
echo 'source <(kubectl completion bash)' >> ~/.bashrc
echo 'alias k=kubectl' >> ~/.bashrc
echo 'alias c=clear' >> ~/.bashrc
echo 'complete -F __start_kubectl k' >> ~/.bashrc
sed -i '1s/^/force_color_prompt=yes\n/' ~/.bashrc


### disable linux swap and remove any existing swap partitions
swapoff -a
sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab


### remove packages
kubeadm reset -f || true
crictl rm --force $(crictl ps -a -q) || true
apt-mark unhold kubelet kubeadm kubectl kubernetes-cni || true
apt-get remove -y docker.io containerd kubelet kubeadm kubectl kubernetes-cni || true
apt-get autoremove -y
systemctl daemon-reload


### install packages
curl https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
cat <<EOF > /etc/apt/sources.list.d/kubernetes.list
deb http://apt.kubernetes.io/ kubernetes-xenial main
EOF
apt-get update
apt-get install -y docker.io containerd kubelet=${KUBE_VERSION}-00 kubeadm=${KUBE_VERSION}-00 kubectl=${KUBE_VERSION}-00 kubernetes-cni
apt-mark hold kubelet kubeadm kubectl kubernetes-cni


### containerd
cat <<EOF | sudo tee /etc/modules-load.d/containerd.conf
overlay
br_netfilter
EOF
sudo modprobe overlay
sudo modprobe br_netfilter
cat <<EOF | sudo tee /etc/sysctl.d/99-kubernetes-cri.conf
net.bridge.bridge-nf-call-iptables  = 1
net.ipv4.ip_forward                 = 1
net.bridge.bridge-nf-call-ip6tables = 1
EOF
sudo sysctl --system
sudo mkdir -p /etc/containerd


### containerd config
cat > /etc/containerd/config.toml <<EOF
disabled_plugins = []
imports = []
oom_score = 0
plugin_dir = ""
required_plugins = []
root = "/var/lib/containerd"
state = "/run/containerd"
version = 2

[plugins]

  [plugins."io.containerd.grpc.v1.cri".containerd.runtimes]
    [plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc]
      base_runtime_spec = ""
      container_annotations = []
      pod_annotations = []
      privileged_without_host_devices = false
      runtime_engine = ""
      runtime_root = ""
      runtime_type = "io.containerd.runc.v2"

      [plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc.options]
        BinaryName = ""
        CriuImagePath = ""
        CriuPath = ""
        CriuWorkPath = ""
        IoGid = 0
        IoUid = 0
        NoNewKeyring = false
        NoPivotRoot = false
        Root = ""
        ShimCgroup = ""
        SystemdCgroup = true
EOF


### crictl uses containerd as default
{
cat <<EOF | sudo tee /etc/crictl.yaml
runtime-endpoint: unix:///run/containerd/containerd.sock
EOF
}


### kubelet should use containerd
{
cat <<EOF | sudo tee /etc/default/kubelet
KUBELET_EXTRA_ARGS="--container-runtime remote --container-runtime-endpoint unix:///run/containerd/containerd.sock"
EOF
}


### install podman
apt-get install software-properties-common -y
add-apt-repository -y ppa:projectatomic/ppa
sudo apt-get -qq -y install podman containers-common
cat <<EOF | sudo tee /etc/containers/registries.conf
[registries.search]
registries = ['docker.io']
EOF


### start services
systemctl daemon-reload
systemctl enable containerd
systemctl restart containerd
systemctl enable kubelet && systemctl start kubelet


### init k8s
kubeadm reset -f
systemctl daemon-reload
service kubelet start

apt-mark unhold kubelet kubeadm kubectl kubernetes-cni

echo
echo "EXECUTE ON MASTER: kubeadm token create --print-join-command --ttl 0"
echo "THEN RUN THE OUTPUT AS COMMAND HERE TO ADD AS WORKER"
echo

```
</details>

## 

```sh
kubeadm token create --print-join-command --ttl 0 #run on master

obtain join token from master and use on worker

kubeadm join 10.154.0.5:6443 --token l7zdhh.l0f1jhl2xgv3qz9t --discovery-token-ca-cert-hash sha256:ee75eca72f80c6f0041c78a86302c3414e9c9dfc495a32678c8e810ce1bd2373 ## run this on worker node (yours will be unique)
```

```sh
connect to cks master

kubectl should be ready! :)
```

### Connect to cluster
```
# install "gcloud" command

# connect "gcloud" to your GCP
gcloud auth login
gcloud projects list
gcloud config set project YOUR_PROJECT

# connect to instance
gcloud compute instances list
gcloud compute ssh cks-master
```

### Open ports
```
gcloud compute firewall-rules create nodeports --allow tcp:30000-40000
```
