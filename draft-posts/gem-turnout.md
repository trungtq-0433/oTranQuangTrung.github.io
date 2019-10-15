Việc config allowed ip khi có sử dụng elb thì cần thêm config nginx nữa để nhận real iP
client -> ip pub elb -> ip private elb -> server
từ bên ngoài khi đi qua elb thì nó đều -> ip private trên card mạng của thằng elb hết ấy a
server nó sẽ chỉ nhìn thấy ip private trong aws thôi chứ k biết được thằng bên ngoài ý anh
nên giwof mình cần get real-ip của mấy bọn client bên ngoài, thì limit ip nó mới ăn được

config nginx
```ruby
        real_ip_header X-Forwarded-For;
        set_real_ip_from 0.0.0.0/0;
```

