# cài đặt vagrant và vào createcentos7
chạy tất cả file vagrant để khởi tạo máy ảo

sau khi tạo xong vào máy ảo cicd để thực hiện kuberspray

# cấp quyền cho user

```
sudo vi /etc/sudoers
```

```
sysadmin        ALL=(ALL)       NOPASSWD: ALL
```

# Kiểm tra tắt swap trên centos hay chưa

```
free -h
```

# Như này là ok rồi nhé

```
total        used        free      shared  buff/cache   available
Mem:           1.8G        236M        1.4G        9.6M        191M        1.4G
Swap:            0B          0B          0B
```
# sau đó tạo thư mục mục đích để chứa kubespray 

```
mkdir kubernetes_installation/
```

# Nếu chưa cài git thì dùng lệnh sau để cài git

```
sudo yum install git 
```

# tại thư mục kubernetes_installation 

```
git clone https://github.com/kubernetes-sigs/kubespray.git --branch release-2.16
```

# cần tạo 1 thư mục inventory riêng của bạn để chưa bộ mẫu kubespray

```
cd /home/sysadmin/kubernetes_installation/kubespray/inventory
```

```
sudo mkdir tu-cluster
```

```
cd ..
```

```
cp -rf inventory/sample inventory/tu-cluster
```

# Tiếp theo cấu hình file host.yaml quan trọng nhất
--- Lưu ý [etcd] không thể là số chẵn set up theo số lẻ (1-3-5-7-9) ---

```
cd /home/sysadmin/kubernetes_installation/kubespray/inventory/tu-cluster
```

```
vi host.yaml
```

```
[all]
master1  ansible_host=172.16.10.100      ip=172.16.10.100
master2  ansible_host=172.16.10.101      ip=172.16.10.101
master3  ansible_host=172.16.10.107      ip=172.16.10.107
worker1  ansible_host=172.16.10.102      ip=172.16.10.102
worker2  ansible_host=172.16.10.103      ip=172.16.10.103
worker3  ansible_host=172.16.10.104      ip=172.16.10.104

[kube-master]
master1
master2
master3

[kube-node]
worker1
worker2
worker3

[etcd]
master1
master2
master3

[k8s-cluster:children]
kube-node
kube-master

[calico-rr]

[vault]
master1
master2
master3
worker1
worker2
worker3
```
# tiếp theo sẽ đổi CNI (Network plugin của k8s):

```
vi /home/sysadmin/kubernetes_installation/kubespray/inventory/tu-cluster/sample/group_vars/k8s_cluster/k8s-cluster.yml
```

# Tìm kiếm tham số 

```
:/kube_network_plugin
```
# Sửa thành:

```
Từ
kube_network_plugin: calico
Thành
kube_network_plugin: flannel
```
# cấu hình cho Ansible mục đích remote vào từng vps để cài kubernetes

```
sudo yum update
curl -fsSL https://get.docker.com/ | sh
```

# cấp quyền cho user dùng docker như root (Không cần dùng sudo)

```
sudo usermod -aG docker sysadmin
```

# Giờ sẽ tạo docker-container từ kubespray, sau đó thực hiện cài k8s cluster từ bên trong container này:

```
docker run --rm -it --mount type=bind,source=/home/sysadmin/kubernetes_installation/kubespray/inventory/tu-cluster,dst=/inventory \
  --mount type=bind,source=/home/sysadmin/.ssh/id_rsa,dst=/root/.ssh/id_rsa \
  --mount type=bind,source=/home/sysadmin/.ssh/id_rsa,dst=/home/sysadmin/.ssh/id_rsa \
  quay.io/kubespray/kubespray:v2.16.0 bash 

```

--- Sau khi chạy lệnh trên, thì chúng ta đã exec vào bên trong container đó rồi, các bạn lưu ý dấu nhắc bây giờ sẽ là "root@b2dfac52ad07:/# ---

# Bây giờ chúng ta chạy lệnh này:

```
ansible-playbook -i /inventory/hosts.yaml cluster.yml --user=sysadmin --ask-pass --become --ask-become-pass
```
# Kết quả như này là đã hoàn thành:

```
PLAY RECAP *****************************************************************************************************************************************************************************
localhost                  : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
       master1             : ok=480  changed=106  unreachable=0    failed=0    skipped=1017 rescued=0    ignored=1
       master2             : ok=448  changed=98   unreachable=0    failed=0    skipped=944  rescued=0    ignored=0
       master3             : ok=450  changed=99   unreachable=0    failed=0    skipped=942  rescued=0    ignored=0
       worker1             : ok=342  changed=73   unreachable=0    failed=0    skipped=647  rescued=0    ignored=0
       worker2             : ok=320  changed=70   unreachable=0    failed=0    skipped=568  rescued=0    ignored=0
       worker3             : ok=320  changed=70   unreachable=0    failed=0    skipped=568  rescued=0    ignored=0

Wednesday 06 April 2022  09:22:13 +0000 (0:00:00.089)       0:48:05.297 *******
===============================================================================
kubernetes/preinstall : Install packages requirements ------------------------------------------------------------------------------------------------------------------------ 2043.59s
download_file | Download item -------------------------------------------------------------------------------------------------------------------------------------------------- 99.24s
kubernetes/control-plane : Joining control plane node to the cluster. ---------------------------------------------------------------------------------------------------------- 41.14s
download_file | Download item -------------------------------------------------------------------------------------------------------------------------------------------------- 35.98s
kubernetes/kubeadm : Join to cluster ------------------------------------------------------------------------------------------------------------------------------------------- 30.79s
download_file | Download item -------------------------------------------------------------------------------------------------------------------------------------------------- 27.33s
kubernetes/control-plane : kubeadm | Initialize first master ------------------------------------------------------------------------------------------------------------------- 24.87s
download_container | Download image if required -------------------------------------------------------------------------------------------------------------------------------- 24.69s
download_container | Download image if required -------------------------------------------------------------------------------------------------------------------------------- 24.61s
download_file | Download item -------------------------------------------------------------------------------------------------------------------------------------------------- 23.56s
download_container | Download image if required -------------------------------------------------------------------------------------------------------------------------------- 23.05s
download_file | Download item -------------------------------------------------------------------------------------------------------------------------------------------------- 20.04s
container-engine/docker : ensure service is started if docker packages are already present ------------------------------------------------------------------------------------- 17.01s
download_container | Download image if required -------------------------------------------------------------------------------------------------------------------------------- 12.00s
download : check_pull_required |  Generate a list of information about the images on a node ------------------------------------------------------------------------------------ 11.73s
prep_download | Register docker images info ------------------------------------------------------------------------------------------------------------------------------------ 10.67s
reload etcd -------------------------------------------------------------------------------------------------------------------------------------------------------------------- 10.42s
download_container | Download image if required --------------------------------------------------------------------------------------------------------------------------------- 8.40s
download : check_pull_required |  Generate a list of information about the images on a node ------------------------------------------------------------------------------------- 7.67s
download_container | Download image if required --------------------------------------------------------------------------------------------------------------------------------- 7.43s

```

# sau đó ta đăng nhập vào các vps master để cấu hình

```
mkdir -p $HOME/.kube
sudo cp /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

# Sau đó kiểm tra thử đã cấu hình thành công chưa

```
[sysadmin@master1 ~]$ kubectl get node -o wide
NAME      STATUS     ROLES                  AGE   VERSION   INTERNAL-IP     EXTERNAL-IP   OS-IMAGE                KERNEL-VERSION           CONTAINER-RUNTIME
master1   Ready      control-plane,master   12h   v1.20.7   172.16.10.100   <none>        CentOS Linux 7 (Core)   3.10.0-1127.el7.x86_64   docker://19.3.15
master2   NotReady   control-plane,master   12h   v1.20.7   172.16.10.101   <none>        CentOS Linux 7 (Core)   3.10.0-1127.el7.x86_64   docker://19.3.15
master3   Ready      control-plane,master   12h   v1.20.7   172.16.10.107   <none>        CentOS Linux 7 (Core)   3.10.0-1127.el7.x86_64   docker://19.3.15
worker1   Ready      <none>                 12h   v1.20.7   172.16.10.102   <none>        CentOS Linux 7 (Core)   3.10.0-1127.el7.x86_64   docker://19.3.15
worker2   Ready      <none>                 12h   v1.20.7   172.16.10.103   <none>        CentOS Linux 7 (Core)   3.10.0-1127.el7.x86_64   docker://19.3.15
worker3   Ready      <none>                 12h   v1.20.7   172.16.10.104   <none>        CentOS Linux 7 (Core)   3.10.0-1127.el7.x86_64   docker://19.3.15
```

# Như này đã thành công thiết lập kubernetes cluster nhiều master và nhiều worker rồi

