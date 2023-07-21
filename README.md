# Test ansible
## 1. Dùng ansible để cài đặt mysql
Ansible là một công cụ quản lý cấu hình mã nguồn mở được sử dụng để tự động hóa việc cài đặt, cấu hình và triển khai các ứng dụng trên nhiều máy chủ. Khi sử dụng Ansible để cài đặt và cấu hình MySQL, ta có thể tự động hóa quy trình cài đặt và cấu hình cơ sở dữ liệu MySQL trên các máy chủ mục tiêu. Điều này giúp tiết kiệm thời gian và đảm bảo sự nhất quán trong việc triển khai và cấu hình của MySQL trên các máy chủ khác nhau.
### Tiến hành các bước:
- Chuẩn bị: 2 máy, 1 máy cài sẵn ansible và 1 máy con
  Ở máy đã cài sẵn ansible:
B1: Đăng nhập vào root: `sudo -i`
B2: Tạo một tệp văn bản với tên hosts (hoặc tên khác) để xác định node mục tiêu của Ansible: `nano hosts`. Sau đó thêm vào tệp nội dung sau:
```
[mysql_servers]
ip máy con 
```
B3: Chuẩn bị tệp playbook cho Ansible
Tạo một tệp văn bản với tên install_mysql.yml (hoặc tên khác) và đưa các hướng dẫn cài đặt và cấu hình MySQL vào tệp này. Nội dung của tệp install_mysql.yml sẽ như sau:
```
---
- hosts: mysql_servers
  become: yes
  tasks:
    - name: Update apt cache
      apt:
        update_cache: yes

    - name: Install MySQL Server
      apt:
        name: mysql-server
        state: present

    - name: Secure MySQL installation
      debconf:
        name: 'mysql-server'
        question: 'mysql-server/root_password'
        value: 'mật khẩu bạn muốn đặt trong mysql'
        vtype: 'password'
      become: yes

    - name: Install Python MySQL library
      apt:
        name: python3-mysqldb
        state: present

    - name: Restart MySQL service
      service:
        name: mysql
        state: restarted

```
B4: Chạy playbook: `ansible-playbook -i hosts install_mysql.yml`
Ansible sẽ thực hiện các bước trong playbook và cài đặt MySQL trên máy chủ Ubuntu con. Kiểm tra cài đặt thành công hay không bằng cách sử dụng máy con đăng nhập vào mysql bằng tài khoản root với mật khẩu ở trên. Nếu vào được là thành công.
