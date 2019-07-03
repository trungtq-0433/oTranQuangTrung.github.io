#### Case1:
Trong shell command ký tự `\` sẽ bị escape khi chạy lệnh.
Để disable escape ta để các ký tự(cụm ký tự) vào trong double qoute.

sudo chown -R "Framgia\tran.quang.trung" .


#### Case2:
Với các shell command có chưa ký tự $. Ví dụ $(id -u).
Khi thực hiện trong Makefile thì ký tự $ bị escape khi chạy lệnh.
Xử lý: Thêm ký tự $ để disable escape
  echo $$(id -u)
hoặc
  @(id -u)
