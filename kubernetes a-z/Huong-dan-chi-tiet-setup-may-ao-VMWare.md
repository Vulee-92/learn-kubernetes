# Hướng dẫn chi tiết

**Cài đặt VMware Workstation Pro**

**---key bản quyền VMware Workstation Pro 16 ---**

ZF3R0-FHED2-M80TY-8QYGC-NPKYF
YF390-0HF8P-M81RQ-2DXQE-M2UT6
ZF71R-DMX85-08DQY-8YMNC-PPHV8
FC11K-00DE0-0800Z-04Z5E-MC8T6

# Cấu hình network cho VMware Workstation Pro

Bước này nhằm mục đích setup dải IP mình sẽ dùng cho các máy ảo (VM) sau này. Để thực hiện, các bạn mở VMware Workstation Pro vào mục edit ==> Virtual Network Editor ==> Chọn vào loại network là NAT:

![alt text](images/image-34.png)

Ở đây mình hướng dẫn sử dụng NAT, khi đó các VM sẽ được cấp một private IP trong dải mà bạn cài đặt ở mục Subnet IP. Các bạn có thể setup dải này tùy ý, ở đây mình recommend dùng dải 192.168.10.0 mask 255.255.255.0 (như hình).

# Hướng dẫn cài đặt Centos7 và cấu hình tham số

**Cài đặt centos7 lên máy ảo (VM)**

Đầu tiên các bạn download bộ cài Centos7 [http://centos-hcm.viettelidc.com.vn/7/isos/x86_64/], nhớ chọn đúng file "CentOS-7-x86_64-Minimal-2009.iso" cho nó nhẹ!