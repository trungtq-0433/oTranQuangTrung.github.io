Makefile là một file dạng script chứa các thông tin:
  - Cấu trúc project (file, sự phụ thuộc)
  - Các lệnh để tạo file
  

Cấu trúc makefile
up: down
    docker-compose up -d
down:
    docker-compose down
  
explain:
up: target
down: dependency
docker-compose up -d: action
new line indent 4 space
