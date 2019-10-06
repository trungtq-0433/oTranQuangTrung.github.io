Query ngày trong rails với trường có data type là datetime.
- Chú ý là ta sẽ pasrse dữ liệu đầu vào và dữ liệu so sánh về kiểu DATE
- Tiếp theo đầu truyền vào thì chúng ta truyền kiểu DateTime để có cả TimeZone. Lúc vào DB thì rails sẽ tự convert về giờ UTC.

```
where "DATE(created_at) = DATE(?)", Time.current)
```
