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
Ansible sẽ thực hiện các bước trong playbook và cài đặt MySQL trên máy chủ Ubuntu con.
  Ở máy con:
Kiểm tra cài đặt thành công hay không bằng cách sử dụng máy con đăng nhập vào mysql bằng tài khoản root với mật khẩu ở trên. Nếu vào được là thành công.

## 2. Dùng ansible để cài đặt wordpress
Ansible cung cấp một kiến trúc đơn giản không yêu cầu cài đặt phần mềm đặc biệt trên các node . Nó cũng cung cấp một bộ tính năng mạnh mẽ và các module tích hợp giúp tạo điều kiện thuận lợi cho việc viết các tập lệnh tự động hóa. WordPress là CMS (hệ thống quản lý nội dung) phổ biến nhất trên internet, cho phép user cài đặt các blog và trang web linh hoạt trên nền backend MySQL với PHP processor . Sau khi cài đặt , hầu hết tất cả các quản trị có thể được thực hiện thông qua giao diện user web.
### Các bước tiến hành:
- Chuẩn bị: 2 máy, 1 máy cài sẵn ansible và 1 máy con
Ở máy đã cài sẵn ansible:
B1: Đăng nhập vào root: `sudo -i`
B2: Sao chép repository vào folder chính với:
```
cd ~ 
git clone https://github.com/do-community/ansible-playbooks.git 
cd ansible-playbooks 
```
Những file có trong ansible-playbooks:
- files/apache.conf.j2 : Tệp mẫu để cài đặt Apache VirtualHost.
- files/wp-config.php.j2 : Tệp mẫu để cài đặt file cấu hình của WordPress.
- vars/default.yml : Tệp biến để tùy chỉnh cài đặt sách vở.
- playbook.yml : Tệp playbook, chứa các việc sẽ được thực thi trên (các) server từ xa.
- readme.md : Tệp văn bản chứa thông tin về sách chơi này.
B2: Chỉnh sửa file biến của playbook để tùy chỉnh các tùy chọn của nó. Truy cập folder wordpress-lamp_ubuntu1804 và mở file vars/default.yml:
```
cd wordpress-lamp_ubuntu1804 
nano vars/default.yml
```
B3: Danh sách sau đây chứa giải thích ngắn gọn về từng biến này và thay đổi chúng:
- php_modules : Một mảng chứa các phần mở rộng PHP cần được cài đặt để hỗ trợ cài đặt WordPress của bạn. Bạn không cần phải thay đổi biến này, nhưng bạn có thể cần bao gồm các tiện ích mở rộng mới vào danh sách nếu cài đặt cụ thể của bạn yêu cầu.
- mysql_root_password : Mật khẩu mong muốn cho account MySQL gốc .
- mysql_db : Tên của database MySQL sẽ được tạo cho WordPress.
- mysql_user : Tên của user MySQL sẽ được tạo cho WordPress.
- mysql_password : Mật khẩu cho user MySQL mới.
- http_host : Tên domain của bạn.
- http_conf : Tên của file cấu hình sẽ được tạo trong Apache.
- http_port : Cổng HTTP cho server ảo này, trong đó 80 là mặc định.
 Lưu file và thoát khỏi trình văn bản.
B4: Chạy playbook: ansible-playbook playbook.yml -l ipmaycon -u root

  Ở máy con:
Vào ip để kiểm tra xem đã thành công hay chưa. Nếu vào ip ra wordpress thì thành công
