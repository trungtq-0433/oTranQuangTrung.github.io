---
layout: post
title:  "Ghi chép gem Ransack"
categories: Gem
author: tqtrung09
---

* content
{:toc}


[Gem Ransack](https://github.com/activerecord-hackery/ransack) đã trở nên quen thuộc với `RoR developer` khi mà nó có mặt hầu như trong các project, bởi vì tính dễ
dàng cài đặt, sử dụng, dễ custom và tiết kiệm công sức developer.
Sau đây là một số ghi chép của mình về gem này...






## `Customs Predicates`

Trên [wiki gem](https://github.com/activerecord-hackery/ransack/wiki/Custom-Predicates) cũng có bài biết về vấn đề này.

Trong ghi chép này của mình thì mình sẽ ghi chép, hướng dẫn cụ thể hơn.

Trong dự án mình làm, phần tìm kiếm `user` mục tìm kiếm theo attribute name, mình đang implement với [search matchers](https://github.com/activerecord-hackery/ransack#search-matchers) là `_cont`.

Câu query được sinh ra như sau: 
```ruby
SELECT `users`.* FROM `users` WHERE `users`.`name` LIKE '%xxx%'
```

Với câu query trên thì các bản ghi có name chứa ký tự `xxx` thì sẽ được liệt kê ra.

Kết quả vẫn đúng theo mình nghĩ tới ngày KH vào test thì mong muốn nhập nội dung `xxx` cũng ra các bản ghi kiểu:
`XXX`, `XxX`, `xxX` ...

Ý tưởng: Mình sẽ upper case hoặc lower case ký tự tìm kiếm và tập giá trị tìm kiếm, sau đó mới thực hiện tim kiếm.

Cũng có nhiều hướng để xử lý trường hợp này, nhưng mình muốn tìm cách nào tổng quát và có thể sử dụng lại đơn giản nhất.

Mình chọn cách là custom thêm 1 cái search matchers. Sau đây là cách làm của mình.

Tại user model ta sử dụng [ranksacker](https://github.com/activerecord-hackery/ransack/wiki/using-ransackers) để định nghĩa lại giá trị cuả thuộc tính name thành chữ viết hoa toàn bộ.

```ruby
ransacker :name do 
  Arel.sql("UPPER(name)")
end

```

Tại `config/initializers/ransack.rb` chúng ta sẽ định nghĩa thêm một search matchers từ các predicate sẵn có của
[Arel](http://www.rubydoc.info/github/rails/arel/master/Arel/Predications)
Ở đây mình sử dụng predicate là [matchers](http://www.rubydoc.info/github/rails/arel/master/Arel%2FPredications:matches)

```ruby
Ransack.configure do |config|
  config.add_predicate "icont", # Đặt tên cho matcher
    arel_predicate: "matches", # Sử dụng base là matchers của Arel
    formatter: proc { |v| "%#{v.upcase}%" }, # Đây là đoạn string mình truyền vào câu query của Arel matchers.
    validator: proc { |v| v.present? }, # Yêu cầu attributes sử dụng matcher phải có giá trị.
    type: :string # Định nghĩa kiểu dữ liệu sử dụng matcher
end
```

Bây giờ ta có thể sử dụng matcher này như bao matchers khác của gem ranksack như sau.
```ruby
= search_form_for @q do |f|
  = f.search_field :name_icont
  = f.submit "Submit"
end
```

## `To be continued`







