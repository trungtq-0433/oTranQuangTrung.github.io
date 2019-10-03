Không đặt mailer vào transaction.
Chỉ được đặt trong block else
Nguyên nhân:
- mailer không rollback được khi raise error
- mailer background job chạy ở thread khác nên khi truyền id, hay 
```
begin
rescue
else
ensure 
end
```
