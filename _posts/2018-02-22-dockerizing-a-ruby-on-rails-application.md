---
layout: post
title: "Dockerizing a ruby on rails application"
categories: Docker
author: tqtrung09
---

* content
{:toc}

Mình biết đến docker từ giữa năm 2016. Thời gian đó, thấy người người ca ngợi docker thần thánh, trong công ty cũng đã sử dụng docker thì mình cũng đua đòi nghịch ngợm chứ chưa xây dựng được một
cái gì ra hồn hết cả, bẵng đi một thời gian đến giữa năm 2017 thì được làm một dự án từ đầu nên mình đã quyết định sử
dụng docker vào dự án. Cứ mỗi khoảng thời gian bỏ phí mình lại thấy tiếc nuối =))




## `Setup`
Chúng ta cần cài đặt 2 thứ đó là `docker` và `docker-compose`
Cách cài đặt  chúng ta cứ theo hướng dẫn từ trang chủ của docker chứ mình sẽ không nói lại ở đây.
[document docker](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
[docker-compose](https://docs.docker.com/compose/install/#install-compose)

Sau khi cài đặt thì `docker` được liên kết qua `Unix socket`. Mặc định thì `Unix socket` được sở hữu bởi user `roor`. Thế
nên 1 user khác chỉ có thể truy cập `docker` bằng quyền `sudo`.
Nếu không muốn sử dụng `sudo` khi xài `docker command` thì chúng ta tạo một `Unix group` là `docker`, sau đó chúng ta thêm user
vào group. Khi `docker` khởi động thì quyền truy cập `Unix socket` được phân quyền cho `docker` group.

```
1.Create the docker group.
$ sudo groupadd docker

2.Add your user to the docker group.
$ sudo usermod -aG docker $USER

3.Log out and log back in so that your group membership is re-evaluated.

4.Verify that you can run docker commands without sudo.
$ docker run hello-world
```

## `Prepare`
- Rails application: Tất nhiên là phải có mới dockerizing được.
Có thể sử dụng rails app sẵn có hoặc tạo new rails app bằng bằng docker và docker composer như
[tutorial](https://docs.docker.com/compose/rails/#build-the-project)

- Dockerfile

- docker-compose.yml

## `Dockerizing`

##### 1.Dockerfile

`touch Dockerfile`

Đầu tiên ta tạo 1 file Dockerfile tại thư mục root rails app.
Chỉnh sửa nội dung như sau.

```ruby
FROM ruby:2.4.2 
MAINTAINER buffalo <tqtrung09@gmail.com>

RUN apt-get update -qq && apt-get install -y build-essential libpq-dev nodejs vim cron

ENV APP_HOME /Lab

RUN mkdir $APP_HOME
WORKDIR $APP_HOME
ADD Gemfile $APP_HOME/Gemfile
ADD Gemfile.lock $APP_HOME/Gemfile.lock
RUN gem install bundler
RUN bundle install
ADD . $APP_HOME
```

Mình sẽ giải thích qua các lệnh trên:

`FROM ruby:2.4.2`: Đây là image base của mình, nó sẽ được tải từ docker hub khi mình build.

`RUN apt-get update -qq && apt-get install -y build-essential libpq-dev nodejs vim cron`: Đây là các gói cài đặt thêm
vào image. Trên đây có `vim` và `cron` là 2 package optional mình cài thêm để sử dụng vim và cronjob trong ứng dụng.

`ENV APP_HOME /LouisVLab`: Set biến thư mục dự án trong remote docker, giống với folder rails app của máy host.

`RUN mkdir $APP_HOME`: Tạo thư mục trong remote.

`WORKDIR $APP_HOME`: Set thư mục làm việc.

`ADD Gemfile $APP_HOME/Gemfile`: Copy Gemfile từ host vào remote.

`ADD Gemfile.lock $APP_HOME/Gemfile.lock`: Copy Gemfile.lock từ host vào remote.

`RUN gem install bundler`: Cài gem bundler cho remote.

`RUN bundle install`: Chạy bundle 

`ADD . $APP_HOME`: Bind thư mục từ remote ra host


##### 2.Docker compose

`touch docker-compose.yml`

Chỉnh sửa file với nội dung như sau.

```ruby
version: "3"
services:
  database:
    image: mysql:5.7
    restart: always
    volumes:
      - ./data/mysql:/var/lib/mysql
    ports: 
      - "3306:3306"
    environment:
      MYSQL_ROOT_PASSWORD: "123456"
  redis:
    image: redis:3.2-alpine
    ports:
      - "6379:6379"
    volumes:
      - ./data/redis:/var/lib/redis/data
  sidekiq:
    build: .
    command: bundle exec sidekiq -q default -q mailers
    volumes:
      - .:/Lab
    depends_on:
      - database
      - redis
    environment: &environment
      RAILS_ENV: "development"
      LV_DATABASE_HOST: "database"
      LV_DATABASE_USER: "root"
      LV_DATABASE_PASSWORD: "123456" 
      REDIS_URL: "redis://redis:6379/0"
  web:
    build: .
    environment:
      <<: *environment
    command: bundle exec rails s -p 3000 -b "0.0.0.0"
    restart: always
    volumes:
      - .:/Lab
    ports:
      - "3000:3000"
    tty: true
    stdin_open: true
    depends_on: 
      - database
      - redis
```

Ở đây mình dùng version 3 của docker-compose
Trong services, chúng ta cần chú ý các service chính sau: `database`, `redis`, `sidekiq`, `web`

```ruby
 database:
    image: mysql:5.7 # Là image được tải từ docker hub
    restart: always # Option khi service bị tắt bới nguyên nhân gì đó sẽ được khởi động lại luôn.
    volumes:
      - ./data/mysql:/var/lib/mysql # mapping dữ liệu từ remote ra máy host.
      # cú pháp <thư mục máy host>:<thư mục remote>
    ports: 
      - "3306:3306" # Bind cổng 3306 từ remote ra cổng 3306 máy host.
    environment:
      MYSQL_ROOT_PASSWORD: "123456" # Set password user root của mysql.
```


```ruby
redis:
    image: redis:3.2-alpine
    ports:
      - "6379:6379"
    volumes:
      - ./data/redis:/var/lib/redis/data
```


```ruby
sidekiq:
    build: . # Chạy tại thư mục root rail app
    command: bundle exec sidekiq -q default -q mailers # command khi chạy sidekiq
    volumes:
      - .:/Lab 
    depends_on: # chỗ này là mình thiết lập sidekiq chạy được khi khởi động được 2 services database và redis
      - database 
      - redis
    environment: &environment # Đây là tạo các biết môi trường và đưa nó vào dictionary với key là environment.
      RAILS_ENV: "development"
      LV_DATABASE_HOST: "database"
      LV_DATABASE_USER: "root"
      LV_DATABASE_PASSWORD: "123456" 
      REDIS_URL: "redis://redis:6379/0"
```


```ruby
web:
    build: .
    environment:
      <<: *environment
    command: bundle exec rails s -p 3000 -b "0.0.0.0"
    restart: always
    volumes:
      - .:/Lab
    ports:
      - "3000:3000"
    tty: true # option được sử dụng cho việc debug code
    stdin_open: true# option được sử dụng cho việc debug code
    depends_on: 
      - database
      - redis
```


Vậy là đã xong phần dockerizing. Tiếp theo chúng ta sẽ đến phần sử dụng.

## `Using`
Để chạy được ứng dụng, chúng ta cần chỉnh sửa config `database.yml`

```ruby
default: &default
  adapter: mysql2
  encoding: utf8mb4
  collation: utf8mb4_bin
  pool: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>
  username: <%= ENV["LV_DATABASE_USER"] %>
  password: <%= ENV["LV_DATABASE_PASSWORD"] %>
  host: <%= ENV["LV_DATABASE_HOST"] %>
  # socket: /var/run/mysqld/mysqld.sock

development:
  <<: *default
  database: LouisVLab_development

# Warning: The database defined as "test" will be erased and
# re-generated from your development database when you run "rake".
# Do not set this db to the same as development or production.
test:
  <<: *default
  database: LouisVLab_test
```

Chú ý: Các biến `username`, `password`, `host` được gán bằng các biến môi trường mình khai báo trong
`docker-compose.yml`

Tại thư mục root rails app chạy các lệnh sau và truy cập localhost:3000 để xem thành quả:

`docker-compose build`

`docker-compose up -d ` 


Dưới đây là các lệnh cơ bản khi thao tác với docker:

1.`docker-compose build`

  Lệnh trên để setup môi trường với những cài đặt của chúng ta khai báo trong docker-compose.yml

2.`docker-compose up -d `
  
  Lệnh này để start environment.

3.`docker-compose down`
  
  Lệnh này để shutdown environment.

4.`docker-compose ps`
  
  Liệt kê các container đang chạy.

5.`docker-compose up -d --build`
  
  Khi chỉnh sửa trong Gemfile thì chúng ta chạy lại lệnh này để build lại môi trường.

6.`docker exec -it containerId /bin/bash`
  
  Sử dụng để truy cập vào 1 container đang hoạt động.

7.`exit`
  
  Sử dụng để thoát khỏi container.


Khi chúng ta có sử dụng `gem pry` hoặc byebug để debug thì chúng ta sử dụng lệnh sau attach vào container để debug.

8.`docker attach containerId`

9.`Ctrl+p` + `Ctrl+q`
  
  Sử dụng khi chúng ta muốn thoát khỏi mode debug. Chú ý không sử dụng `Ctrl+c` để thoát.

Thỉnh thoảng chúng ta sẽ gặp lỗi và chúng ta sử dụng lệnh sau để đọc log docker.

10.`docker logs containerId`

