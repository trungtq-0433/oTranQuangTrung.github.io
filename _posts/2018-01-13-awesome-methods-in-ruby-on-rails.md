---
layout: post
title:  "Awesome methods in Ruby on rails"
categories: Awesome
author: tqtrung09
---

* content
{:toc}


Làm việc với ngôn ngữ ruby và framework RubyOnRails một thời gian. Tôi nhận thấy những đoạn code xử lý ngày đầu vào nghề có vấn đề và thấy mình cần phải cải tiến...





## `Hash#dig`
require: ruby2.3

Ví dụ:
```ruby
if params[:user] && params[:user][:location] && params[:user][:location][:province]
  do_something
end
```
Đây là đoạn code khá dễ thấy, chúng ta thấy nhiều trong việc kiểm tra tồn tại.

=> Trông thì rõ nghĩa nhưng rối rắm và không đẹp mắt.

Với việc sử dụng `dig method` đoạn code trên sẽ refactor được như sau.
```ruby
if params.dig(:user, :location, :province)
  do_something
end
```
Method `dig` trả về `value` của `key` hoặc trả về `nil` nếu `key` không tồn tại.
Lưu ý: Nếu giá trị kiểm tra của bạn có thể chứa giá trị `""` hoặc `" "` thì lúc kiểm tra điều kiện if cần thêm `.presence`


## `Object#presence_in`
Chúng ta xem đoạn code sau.
```ruby
settings.yml

user:
  types: [:ceo, :magager, :group_leader, :employee]
```

```ruby
type = Settings.user.types.include?(params[:type]) ? params[:type] : :guest
```
Mục đích đoạn code trên là để trả về 1 giá trị thỏa mãn tập cho trước, còn không thì trả về giá trị mặc định.

=> Cũng đã khá rõ ràng rồi, nhưng chúng ta cũng có thể viết lại bằng cách sử dụng method `presence_in` như sau.
```ruby
type = params[:type].precense_in(Settings.user.types) || :guest
```

## `Array#zip`
Chúng ta có dữ liệu như sau.
```ruby
a = [:a, :b, :c]
b = [1, 2, 3]
```
Yêu cầu làm sao tạo thành 1 `Hash` có cấu trúc như sau:
```ruby
{
  "a": 1,
  "b": 2,
  "c": 3
}
```

Theo lối mòn tư duy, ta có thể giải quyết bài toán như sau.
```ruby
hash = Hash.new
a.each.with_index(0) do |_, i|
  hash.merge!({a[i] => b[i]})
end
hash
```
Song khá lằng nhằng và rối rắm. Ta giải quyết bài toán này bằng `zip method` như sau.
```ruby
 hash = Hash.new a.zip(b)
```

## `Object#tap`
Đã bao giờ bạn khởi tạo 1 object, thay đổi object đó và trả về chính đối tượng đó kiểu như thế này?

```ruby
user = User.new

user.name = "buffalo"

user
```
Việc sử dụng biến tạm ta thấy code không đẹp.
```ruby
User.new.tap { |user| user.name = "buffalo" }
```
Với `tap method` thì đoạn code trên đã trở nên gọn gàng và đẹp hơn rất nhiều.

## `Array#count`
Ví dụ chúng ta có.
```ruby
array = %w(a b c d d c b a c c d d b)
```
Chúng ta muốn đếm số lần xuất hiện của các ký tự trong mảng, ví dụ ký tự `d`.
Theo cách truyền thống chúng ta thực hiện vòng lặp và so sánh giá trị như sau.

```ruby
count = 0
array.each do |c|
  if c == "d"
    count += 1
  end
end
count
```
Và sau đây là sử dụng `count method`
```ruby
array.count("d")
```
Quá là đơn giản phải không ạ.

Hoặc 1 cách áp dụng khác với mức độ phức tạp hơn 1 chút: Tìm số chẵn, lẻ
```ruby
array = [1,2,3,4,5]
array.count(&:even?)
```

## `Conclusion`
Bài viết là phần tổng hợp các method mình đã rút ra được để áp dụng trong quá trình làm việc. Mình sẽ cập nhật tiếp các
method khác vào bài viết này nếu thấy ứng dụng được hay ho.

Mọi người có thể chia sẻ các method thú vị mà các bạn đã sử dụng trong quá trình làm việc ở phần dưới comments để mình
được học hỏi.
