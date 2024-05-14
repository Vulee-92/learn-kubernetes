dùng lệnh để cáp full quyền cho sysadmin để dùng ansible cài đặt
sudo visudo
sysadmin        ALL=(ALL)       NOPASSWD: ALL
vào máy VM cicd
cài đặt git để clone repository:
sudo yum install git