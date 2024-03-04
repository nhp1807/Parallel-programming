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

### Bước 2: Cài đặt NFS server trên máy master và NFS client trên các máy slaves (Network File System)
- Trên máy master, ta cài đặt NFS master
```ssh
sudo apt install nfs-kernel-server
```

- Tạo thư mục chung cho việc chia sẻ file
```
sudo mkdir -p /home/mpiuser/Desktop/sharedfolder
```

- Thay đổi quyền của folder dùng chung
```
sudo chown nobody:nogroup /home/mpiuser/Desktop/sharedfolder
sudo chmod 777 /home/mpiuser/Desktop/sharedfolder
```

- Sửa file /etc/exports, thêm 2 dòng sau
```
/home/mpiuser/Desktop/sharedfolder <slave1IP>(rw, sync, no_subtree_check)
/home/mpiuser/Desktop/sharedfolder <slave2IP>(rw, sync, no_subtree_check)
/home/mpiuser/Desktop/sharedfolder <slave3IP>(rw, sync, no_subtree_check)
```

- Sau đó exports thư mục dùng chung
```
sudo exports -a
```

- Restart NFS server
```
sudo systemctl restart nfs-kernel-server
```

- Kiểm tra firewall
```
sudo ufw status
```

- Trên máy slave, ta cài đặt NFS client 
```ssh
sudo apt install nfs-common
```

- Tạo thư mục 
```
sudo mkdir -p /home/mpiuser/Desktop/sharedfolder
```

- Mount shared folder (tại các slave) to sharedfolder (tại master)
```
sudo mount <serverIP>:/home/mpiuser/Desktop/sharedfolder /home/mpiuser/Desktop/sharedfolder
```

### Bước 3: Cài đặt OpenMPI
- Cài đặt gcc cho tất cả các máy
```
sudo apt install gcc
```

- Cài đặt một vài thư viện cần thiết
```
sudo apt install openmpi-bin openmpi-common libopenmpi-dev libgtk2.0-dev
```

- Download và giải nén openmpi cho các máy, truy cập open-mpi.org, download open-mpi, [openmpi-5.0.2.tar.gz](https://download.open-mpi.org/release/open-mpi/v5.0/openmpi-5.0.2.tar.gz), sau đó copy file này tới /home/mpiuser/Desktop, sau đó tới thư mục /home/mpiuser/Desktop và giải nén
```
tar -xvf /home/mpiuser/Desktop/<file_name>
```

- Truy cập /home/mpiuser/Desktop/openmpi-<version>, sau đó chạy 3 lệnh
```
./configure --prefix="/home/mpiuse/.openmpi"
make
sudo make install
```
- Export PATH
```
export PATH="$PATH:/home/mpiuser/.openmpi/bin"
export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:/home/mpiuser/.openmpi/lib"
```

- Test bằng cách chạy mpirun hoặc mpicc (Hệ thống biết có lệnh mpirun là được)
```
mpirun
```


