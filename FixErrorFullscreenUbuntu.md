# Fix lỗi terminal cho ubuntu
setting -> region and language -> chọn English (Canada)

chọn region -> Singapo
# Fix lỗi Full screen cho ubuntu
Devices -> Insert Gsest Addition CD images

sau đó click vào đĩa CD đã tải xuống -> mở terminals

```
findmnt
```

```
/media/patu/VBox_Gas_6.1.34/VBoxLinuxAdditions.run
```

```
su root 
```

```
nano /etc/sudoers
```

```
user_name ALL=(ALL)  ALL
```

```
sudo /media/patu/VBox_Gas_6.1.34/VBoxLinuxAdditions.run
```

```
sudo apt-get update
```

```
sudo apt-get install build-essential gcc make perl dkms
```

```
sudo /media/patu/VBox_Gas_6.1.34/VBoxLinuxAdditions.run
```

```
sudo reboot
```
# Hướng dẫn cài k8s trên ubuntu

UBUNTU SERVER LTS 22.04.3

```
https://ubuntu.com/download/server
```

KUBERNETES 1.29.1      

```
https://kubernetes.io/releases/
```

CONTAINERD 1.7.13

```
https://containerd.io/releases/
```

RUNC 1.1.12    

```
https://github.com/opencontainers/runc/releases
```
CNI PLUGINS 1.4.0   

```
https://github.com/containernetworking/plugins/releases
```
CALICO CNI 3.27.2   

```
https://docs.tigera.io/calico/3.27/getting-started/kubernetes/quickstart
```

3 NODES, 2 vCPU, 8 GB RAM, 50GB Disk EACH

k8s-control   192.168.15.93

k8s-1         192.168.15.94

k8s-2         192.168.15.95


### ALL: 

```
sudo su
```

```
printf "\n192.168.15.93 k8s-control\n192.168.15.94 k8s-1\n192.168.15.95 k8s-1\n\n" >> /etc/hosts
```

```
printf "overlay\nbr_netfilter\n" >> /etc/modules-load.d/containerd.conf
```

```
modprobe overlay\
modprobe br_netfilter
```

```
printf "net.bridge.bridge-nf-call-iptables = 1\nnet.ipv4.ip_forward = 1\nnet.bridge.bridge-nf-call-ip6tables = 1\n" >> /etc/sysctl.d/99-kubernetes-cri.conf
```

```
sysctl --system
```

```
wget https://github.com/containerd/containerd/releases/download/v1.7.13/containerd-1.7.13-linux-amd64.tar.gz -P /tmp/
```

```
tar Cxzvf /usr/local /tmp/containerd-1.7.13-linux-amd64.tar.gz
```

```
wget https://raw.githubusercontent.com/containerd/containerd/main/containerd.service -P /etc/systemd/system/
```
```
systemctl daemon-reload
```

```
systemctl enable --now containerd
```

```
wget https://github.com/opencontainers/runc/releases/download/v1.1.12/runc.amd64 -P /tmp/
```

```
install -m 755 /tmp/runc.amd64 /usr/local/sbin/runc
```

```
wget https://github.com/containernetworking/plugins/releases/download/v1.4.0/cni-plugins-linux-amd64-v1.4.0.tgz -P /tmp/
```

```
mkdir -p /opt/cni/bin
```

```
tar Cxzvf /opt/cni/bin /tmp/cni-plugins-linux-amd64-v1.4.0.tgz
```

```
mkdir -p /etc/containerd
```

```
containerd config default | tee /etc/containerd/config.toml
```   
<<<<<<<<<<< manually edit and change SystemdCgroup to true (not systemd_cgroup)

```
nano /etc/containerd/config.toml
```

```
systemctl restart containerd
```

```
swapoff -a
```  
<<<<<<<< just disable it in /etc/fstab instead

```
nano /etc/fstab
``` 
(# để comment)

```
apt-get update
```

```
apt-get install -y apt-transport-https ca-certificates curl gpg
```

```
mkdir -p -m 755 /etc/apt/keyrings
```

```
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.29/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
```

```
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.29/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
```

```
apt-get update
```

```
reboot
```

```
sudo su
```

# check swap config, ensure swap is 0

```
free -m
```

```
apt-get install -y kubelet=1.29.1-1.1 kubeadm=1.29.1-1.1 kubectl=1.29.1-1.1
```

```
apt-mark hold kubelet kubeadm kubectl
```

### ONLY ON CONTROL NODE .. control plane install:

```
kubeadm init --pod-network-cidr 10.10.0.0/16 --kubernetes-version 1.29.1 --node-name k8s-control
```

```
mkdir -p $HOME/.kube
```

```
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
```

```
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

```
export KUBECONFIG=/etc/kubernetes/admin.conf
```

# add Calico 3.27.2 CNI 

```
kubectl create -f https://raw.githubusercontent.com/projectcalico/calico/v3.27.2/manifests/tigera-operator.yaml
```

```
wget https://raw.githubusercontent.com/projectcalico/calico/v3.27.2/manifests/custom-resources.yaml
```

```
nano custom-resources.yaml
``` 
<<<<<< edit the CIDR for pods if its custom(10.10.0.0)

```
kubectl apply -f custom-resources.yaml
```

# get worker node commands to run to join additional nodes into cluster

```
kubeadm token create --print-join-command
```

###


### ONLY ON WORKER nodes

```
Run the command from the token create output above
```

# để gọi k9s trực tiếp từ terminal

```
sudo apt update
```

```
sudo apt install snapd
```

```
sudo snap install k9s
```

```
sudo ln -s /snap/k9s/current/bin/k9s /snap/bin/
```

# Cài đặt jenkins trên centos

 ```
sudo wget -O /etc/yum.repos.d/jenkins.repo \
    https://pkg.jenkins.io/redhat-stable/jenkins.repo
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key
```

```
sudo yum upgrade
```

# Add required dependencies for the jenkins package

--- kiểm tra phiên bản java trên centos --- 

```
yum search java | grep "1.8"
```

--- cài đặt theo như phiên bản trên centos ---

```
sudo yum install java-1.8.0-amazon-corretto.x86_64 
```

```
sudo yum install jenkins
```

```
sudo systemctl daemon-reload
```

# cài đặt jenkins trên ubuntu

```
sudo hostnamectl set-hostname Jenkins
```

```
sudo reboot
```

```
sudo apt update
```

```
sudo apt install openjdk-17-jdk -y
```

```
sudo apt install maven -y
```

```
curl -fsSL https://pkg.jenkins.io/debian/jenkins.io-2023.key | sudo tee \
  /usr/share/keyrings/jenkins-keyring.asc > /dev/null
```

```
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
```

```
sudo apt update
```

```
sudo apt install jenkins -y
```
--- add group docker ---

```
sudo usermod -aG docker $USER
```

--- get password ---

```
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```