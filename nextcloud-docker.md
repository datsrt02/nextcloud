Các bước cài đặt nextcloud trên docker

**B1: tạo thư mục:**
mkdir nextcloud-docker
cd nextcloud-docker

**B2: Tạo docker-compose.yml:**

version: '3'

services:
  db:
    image: mariadb:10.6 # Hoặc postgres:13 nếu bạn muốn dùng PostgreSQL
    command: --transaction-isolation=READ-COMMITTED --binlog-format=ROW # Khuyến nghị cho Nextcloud với MariaDB
    restart: always
    volumes:
      - db_data:/var/lib/mysql # Thay đổi thành db_data:/var/lib/postgresql/data nếu dùng PostgreSQL
    environment:
      MYSQL_ROOT_PASSWORD: your_root_password_here # Đổi mật khẩu mạnh
      MYSQL_PASSWORD: your_db_user_password_here # Đổi mật khẩu mạnh
      MYSQL_DATABASE: nextcloud # Tên cơ sở dữ liệu
      MYSQL_USER: nextclouduser # Tên người dùng cơ sở dữ liệu

  app:
    image: nextcloud:latest
    restart: always
    ports:
      - 8080:80 # Bạn có thể đổi cổng 8080 thành cổng khác nếu muốn
    links:
      - db
    volumes:
      - nextcloud_data:/var/www/html
    environment:
      MYSQL_HOST: db # Tên dịch vụ cơ sở dữ liệu trong Docker Compose
      MYSQL_DATABASE: nextcloud
      MYSQL_USER: nextclouduser
      MYSQL_PASSWORD: your_db_user_password_here
      # NEXTCLOUD_UPDATE: 1 # Bỏ comment nếu muốn tự động cập nhật

volumes:
  db_data: # Tên volume để lưu dữ liệu cơ sở dữ liệu
  nextcloud_data: # Tên volume để lưu dữ liệu Nextcloud

**  B3: Chạy docker**
docker compose up -d
