---
layout: post
title:  "Upload file to s3 using aws-sdk"
categories: rails
author: tqtrung09
---

* content
{:toc}

### Requirement
Cần upload file lên S3, chúng ta cần làm th?




### Implement
Có nhiều gem hỗ trợ việc tương tác với AWS như [fog-aws](https://github.com/fog/fog-aws) tuy nhiên mình sử dụng gem [aws-sdk-ruby](https://github.com/aws/aws-sdk-ruby)
Không biết phải mê tín không nhưng mình thấy xài đồ của official xịn xò hơn, như tốc độ upload file lên s3 của `aws-sdk-ruby` nhanh hơn `fog-aws` khoảng 1-2s.

`aws-sdk` là gem chứa toàn bộ dịch vụ AWS  hỗ trợ. Với yêu cầu upload lên S3 thì chúng ta chỉ cần sử dụng sử dụng 1 phần trong gem, đó là gem `aws-sdk-s3`

```ruby
gem 'aws-sdk-s3', '~> 1'
```
**Configurations**
```ruby
config/initializers/aws_sdk.rb
Aws.config.update({
  region: ENV["REGION"],
  endpoint: ENV["END_POINT"],
  credentials: Aws::Credentials.new(ENV["AWS_ACCESS_KEY_ID"], ENV["AWS_SECRET_ACCESS_KEY"])
})
```
**Using**
```ruby
class S3Storage
  BUCKET = "bucket_name".freeze
  PERMISSION = "public-read".freeze

  attr_reader :file

  def initialize file
    @file = file
  end

  def upload
	# Set file name
    object = bucket.object("folder_name/#{SecureRandom.uuid}")
    # Upload to S3
    object.put(
      acl: PERMISSION,
      body: File.open(file.path)
    )
    # return public url for access
    object.public_url
  end

  private
  # Get bucket by name
  def bucket
    s3 = Aws::S3::Resource.new
    s3.bucket BUCKET
  end
end
```

Tùy vào từng yêu cầu cụ thể hoặc muốn thêm các option khác nhau thì chúng ta vào document để tìm hiểu thêm.



