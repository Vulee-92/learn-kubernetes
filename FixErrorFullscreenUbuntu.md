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

# Fix lỗi caliso cho phiên bản mới
kubectl create -f https://raw.githubusercontent.com/projectcalico/calico/v3.26.0/manifests/tigera-operator.yaml
kubectl create -f https://raw.githubusercontent.com/projectcalico/calico/v3.26.0/manifests/custom-resources.yaml
# để gọi k9s trực tiếp từ terminal
sudo ln -s /snap/k9s/current/bin/k9s /snap/bin/
