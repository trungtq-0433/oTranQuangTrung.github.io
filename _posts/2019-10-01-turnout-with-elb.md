Trong quá trình phát triển 1 ứng dụng web. Chắc chắn có lúc chúng ta cần implement chức năng maintenance mode.
Có 1 số cách khác nhau để có thể implement chức năng này ở tầng web hoặc tầng app.
Trong bài này chúng ta sẽ nói về việc implement ở tầng app.

Để implement tầng app, chúng ta sử dụng gem [Turnout](https://github.com/biola/turnout)

### Cơ bản
#### Cài đặt
```
gem "turnout"

bundle install
```

#### Sử dụng
```
rake maintenance:start
```

#### Cơ chế
[Cơ chế](https://github.com/biola/turnout#behind-the-scenes) của gem Turnout là sẽ tạo ra 1 file maintenance.yml

App sẽ check nếu có tồn tại file đó, thì sẽ bật maintenance mode với config trong file.
Nếu xóa file đi thì chế maintenance mode sẽ được tắt đi.

### Nâng cao
Cơ bản chỉ bật maintenance mode thì như trên là đủ. Tuy nhiên để custom nhiều hơn thì ta thêm các config sau.
```
app/config/initializers/turnout.rb

Turnout.configure do |config|
  # Thư mục app root
  config.app_root = '.'
  # Nơi chứa file maintenance
  config.named_maintenance_file_paths = {default: config.app_root.join('tmp', 'maintenance.yml').to_s}
  # File view cho maintenance mode
  config.maintenance_pages_path = config.app_root.join('public').to_s
  # Loại trang maintenance
  config.default_maintenance_page = Turnout::MaintenancePage::HTML
  # Lý do sẽ hiển thị ở trang maintenance
  config.default_reason = "The site is temporarily down for maintenance.\nPlease check back soon."
  # Cho phép truy cập vào những path nào
  config.default_allowed_paths = []
  # Cho phép dải ip có thể truy cập trong maintenance mode.
  config.default_allowed_ips = []
  # Html code response
  config.default_response_code = 503
  config.default_retry_after = 7200
end
```
Theo như module Turout định nghĩa thì ta có các options sau để config.
https://github.com/biola/turnout/blob/9c57f9d75e31904bc345ecceb9ec392ce3fdb634/lib/turnout/configuration.rb

### ELB
#### With capistrano
Khi deploy ứng dụng lên AWS có kiến trúc load balancer thì việc bật maintenance mode nên đẩy vào capistrano để có thể xử lý multi server.

```ruby
deploy.rb

namespace :maintenance do
  desc "Maintenance start"
  task :start do
    on roles(:app) do
      within release_path do
        execute :rake, "maintenance:start"
      end
    end
  end

  desc "Maintenance stop"
  task :stop do
    on roles(:app) do
      within release_path do
        execute :rake, "maintenance:end"
      end
    end
  end
end
```

#### Allow ips on EC2
Khi sử dụng kiến trúc load balencer thì request ở bên ngoài khi đến `EC2` thì sẽ không còn là ip từ client nữa.
Luồng chạy sẽ như sau:
```
iP client -> ip public elb -> ip private elb -> server EC2
```
Thế nên khi config `default_allowed_ips` cho `Turnout` thì sẽ không hoạt động được.

Để xử lý trường hợp này thì chúng ta cần config ở `Nginx` để có thể lấy real ip từ client.

```
/etc/nginx/nginx.conf
Add following inside http {..} block

real_ip_header X-Forwarded-For;
set_real_ip_from 0.0.0.0/0;
```

Hoặc để nâng cao độ an toàn thì chúng ta nên set
```
set_real_ip_from <ip ELB>
```

