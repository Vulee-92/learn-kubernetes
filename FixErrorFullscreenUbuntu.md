# Fix lỗi terminal cho ubuntu
setting -> region and language -> chọn English (Canada)
chọn region -> Singapo
# Fix lỗi Full screen cho ubuntu
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
