https://github.com/carrierwaveuploader/carrierwave#providing-a-default-url
+ Với carrierwave thì việc lưu file và lưu bản ghi vào DB là 2 tiến trình khác nhau.
Nên sẽ có việc file bị xóa trong thư mục /tmp mà vẫn có bản ghi lưu vào DB.

+ Việc sử dụng trên load blancing thì không được sử dụng cache ở thư mục /tmp mà sử dụng cache ở S3.
https://github.com/carrierwaveuploader/carrierwave/pull/1312
```ruby
CarrierWave.configure do |config|
  config.cache_storage = :fog # default is :file
  config.storage = :fog
end
```
