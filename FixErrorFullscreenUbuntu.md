# Fix lỗi terminal cho ubuntu
setting -> region and language -> chọn English (Canada)

chọn region -> Singapo
# Fix lỗi Full screen cho ubuntu
Devices -> Insert Gsest Addition CD images

sau đó click vào đĩa CD đã tải xuống -> mở terminals

findmnt

/media/patu/VBox_Gas_6.1.34/VBoxLinuxAdditions.run

su root 

nano /etc/sudoers

user_name ALL=(ALL)  ALL

sudo /media/patu/VBox_Gas_6.1.34/VBoxLinuxAdditions.run

sudo apt-get update

sudo apt-get install build-essential gcc make perl dkms

sudo /media/patu/VBox_Gas_6.1.34/VBoxLinuxAdditions.run

sudo reboot
# Hướng dẫn cài k8s trên ubuntu

 curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
 
 curl -LO https://dl.k8s.io/release/v1.30.0/bin/linux/amd64/kubectl
 
 curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl.sha256"
 
 echo "$(cat kubectl.sha256)  kubectl" | sha256sum --check
 
 sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
 
 kubectl version --client
 
 kubectl version --client --output=yaml
 
 sudo apt-get update
 
 sudo apt-get install -y apt-transport-https ca-certificates curl
 
 curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.30/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
 
 sudo chmod 644 /etc/apt/keyrings/kubernetes-apt-keyring.gpg
 
 echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.30/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
 
 sudo chmod 644 /etc/apt/sources.list.d/kubernetes.list
 
 sudo apt-get update
 
 sudo apt-get install -y kubectl
 
 snap install kubectl --classic
 
 kubectl version --client
 
 ---Tắt tường lửa---
 
 systemctl disable firewalld >/dev/null 2>&1
 
 systemctl stop firewalld
 
 ---cài docker---
 
 sudo apt update
 
 sudo apt install docker.io
 
 sudo systemctl start docker
 
 sudo systemctl enable docker
 
 sudo apt install kubeadm kubelet kubectl kubernetes-cni
 
 sudo swapoff -a
 
 sudo nano /etc/fstab
 
 ---sysctl---
 
 sudo bash -c 'cat >> /etc/sysctl.d/kubernetes.conf <<EOF
 
 net.bridge.bridge-nf-call-ip6tables = 1
 
 net.bridge.bridge-nf-call-iptables = 1
 
 EOF'

 ---sau đó comment dòng---
 
 bằng cách thêm dấu # trước đầu dòng và lưu lại
 
 #/swapfile
 
 sudo hostnamectl set-hostname kubernetes-master
 
 ---kiểm tra IP--
 
 hostname -I
 
 10.0.2.15
 
 sudo kubeadm init --apiserver-advertise-address=10.0.2.15 --pod-network-cidr=192.168.0.0/16
 
 mkdir -p $HOME/.kube
 
 sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
 
 sudo chown $(id -u):$(id -g) $HOME/.kube/config
 
 sudo bash -c 'cat >>/etc/NetworkManager/conf.d/calico.conf<<EOF
 
[keyfile]

unmanaged-devices=interface-name:cali*;interface-name:tunl*

EOF'

sudo systemctl restart containerd.service

sudo systemctl restart kubelet.service

sudo systemctl enable kubelet.service

# Fix lỗi caliso cho phiên bản mới

kubectl create -f https://raw.githubusercontent.com/projectcalico/calico/v3.26.0/manifests/tigera-operator.yaml

kubectl create -f https://raw.githubusercontent.com/projectcalico/calico/v3.26.0/manifests/custom-resources.yaml

# để gọi k9s trực tiếp từ terminal

sudo apt update

sudo apt install snapd

sudo snap install k9s

sudo ln -s /snap/k9s/current/bin/k9s /snap/bin/
