Query ngày trong rails với trường có data type là datetime.
- Chú ý là ta sẽ pasrse dữ liệu đầu vào và dữ liệu so sánh về kiểu DATE
- Tiếp theo đầu truyền vào thì chúng ta truyền kiểu DateTime để có cả TimeZone. Lúc vào DB thì rails sẽ tự convert về giờ UTC.

```
where created_at: Time.current.beginning_of_day..Time.current.end_of_day)
```
Không truyền Date, DateTime trực tiếp trong validates model
Chuyển xuống hàm rồi gọi

Không đặt Time ở constant.

Được sử dụng T
