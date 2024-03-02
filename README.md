# Lập trình song song

> Hệ thống sẽ bao gồm 1 máy master và 3 máy slaves

## Các bước cài đặt
### Bước 1: Cài đặt và thiết lập ssh
- Cập nhật các máy trong hệ thống bằng cách thực hiện các câu lệnh
```ssh
sudo apt update
sudo apt upgrade
```
- Sau khi chạy xong, các máy trong  hệ thống đã được cập nhật
- Tiếp theo ta cần phải lấy được địa chỉ IP của mỗi máy
```ssh
sudo apt install tool-nets
```
- Sau khi cài đặt, chúng ta thực hiện lấy IP của máy bằng cách thực hiện câu lệnh
```ssh
ifconfig
```
- Thực hiện cài đặt ssh server và ssh client trên các máy
```ssh
sudo apt install openssh-server
sudo apt install openssh-client
```
- Sau khi cài đăt ssh, ta thực hiện sinh khóa rsa bằng cách thực hiện câu lệnh

```ssh
ssh-keygen -t rsa
```

- Nhập folder muốn lưu trữ khóa, nếu muốn để mặc định thì ấn Enter
- Nhập mật khẩu khóa (không bắt buộc)
- Sao chép các file id_rsa_xxx.pub của các slave vào folder ~/.ssh/ vào máy master
- Sao chép file id_rsa_master.pub của máy master vào folder ~/.ssh/ vào các máy slaves
- Sau khi đã sao chép vào, mỗi máy slave sẽ có 3 files và máy master sẽ có 5 files
- Trong máy master, thực hiện các câu lệnh

```ssh
cat /home/mpiuser/.ssh/id_rsa_slaves1.pub >> /home/mpiuser/.ssh/authorized_keys
cat /home/mpiuser/.ssh/id_rsa_slaves2.pub >> /home/mpiuser/.ssh/authorized_keys
cat /home/mpiuser/.ssh/id_rsa_slaves3.pub >> /home/mpiuser/.ssh/authorized_keys
```

- Đối với các máy slave, thực hiện câu lệnh
```ssh
cat /home/mpiuser/.ssh/id_rsa_master.pub >> /home/mpiuser/.ssh/authorized_keys
```

- Sử dụng trình soạn thảo văn bản bất kỳ (nano, gedit, vi, ...) để thực hiện thay đổi file config trong đường dẫn /etc/ssh/sshd_config
- Thêm 2 dòng sau vào trong file và lưu lại

```ssh
PubkeyAuthentication yes
RSAAuthentication yes
```

- Sau khi thực hiện, các máy restart lại ssh 

```ssh
service ssh restart
```

- Tiếp theo ta thực hiện ssh vào các máy
```ssh
ssh mpiuser@<ip_address>
```
- Ta nhập mật khẩu của user và mật khẩu khóa (nếu cần)

### Bước 2: 

