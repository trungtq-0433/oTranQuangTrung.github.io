---
layout: post
title:  "ActiveRecord's queries tricks"
categories: RoR
author: translater
---

* content
{:toc}

Mảng kiến thức yêu thích của tôi trong framework rails đó là ActiveRecord's scopes.

Trong bài viết này tôi sẽ giới thiệu 5 trick mà tôi thường xuyên sử dụng trong các project của mình.






### 1) Join query with condition on the associated table
Ví dụ ta có bảng `users` với quan hệ với bảng `profile`. Bây giờ chúng ta muốn query các `users` mà `profile` đã được `activated`

```ruby
# User model
scope :activated, ->{
  joins(:profile).where(profiles: { activated: true })
}
```
Tôi nghĩ cách tiếp cận như trên là chưa đúng, logic trong model `Profile` được xuất hiện trong `User` model.
Như vậy là không thỏa mãn tính bao đóng trong `OOP`.
Tôi sẽ đưa ra 1 cách tiếp cận khác.

```ruby
# Profile model
scope :activated, ->{ where(activated: true) }
# User model
scope :activated, ->{ joins(:profile).merge(Profile.activated) }
```

### 2) Different nested joins
Tương tự ở ví dụ trên, chúng ta có thêm quan hệ `Profile` has_many `Skills`. Mặc định khi `joins` là sẽ sử dụng `INNER
JOIN` tuy nhiên ....

```ruby
User.joins(:profiles).merge(Profile.joins(:skills))
=> SELECT users.* FROM users 
   INNER JOIN profiles    ON profiles.user_id  = users.id
   LEFT OUTER JOIN skills ON skills.profile_id = profiles.id

# So you'd rather use:
User.joins(profiles: :skills)
=> SELECT users.* FROM users 
   INNER JOIN profiles ON profiles.user_id  = users.id
   INNER JOIN skills   ON skills.profile_id = profiles.id
```

### 3) Exist query
Ví dụ chúng ta muốn lấy ra toàn bộ các `User` mà các user đó có các post trên 1000 view. 
Chúng ta có thể sử dụng `EXISTS` hoặc `NOT EXISTS`

```ruby
# Post
scope :famous, ->{ where("view_count > ?", 1_000) }

# User
scope :without_famous_post, ->{
  where(_not_exists(Post.where("posts.user_id = users.id").famous))
}

def self._not_exists(scope)
  "NOT #{_exists(scope)}"
end

def self._exists(scope)
  "EXISTS(#{scope.to_sql})"
end
```

### 4) Subqueries
Ví dụ chúng ta muốn lấy các `posts` được viết bởi 1 tập các `users`.
Tôi đã thấy nhiều người sử dụng cách sau.

```ruby
Post.where(user_id: User.created_last_month.pluck(:id))

```
Tuy nhiên với câu truy vấn trên sẽ thực thi 2 câu sql, thứ nhất là lấy ra mảng các user, sau đó là lấy ra các post thỏa mãn yêu cầu.

Chúng ta có thể lấy ra kết quả tương đương chỉ với 1 câu truy vấn.

```ruby
Post.where(user_id: User.created_last_month)

```

### 5) Back to basics
Trong ActiveRecord chúng ta đừng quên là có các phương thức `.sql` để tự động sinh ra câu sql.
Và `.explain` để hiển thị chi tiết thông tin câu truy vấn.

### 6) Booleans
Ví dụ ta mong muốn.
`User.where.not(tall: true)` sẽ sinh ra câu truy vấn như sau `SELECT users.* FROM users WHERE users.tall <> 't'` (postgres version).

Kết quả câu truy vấn trả về những user mà `tall` được set là `false` tuy nhiên những user nào mà `tall` set bằng `NULL` thì không được đưa vào tập kết quả.

Chúng ta có thể set `default` cho trường `tall` trong database hoặc sửa lại câu truy vấn như sau.
```ruby
User.where("users.tall IS NOT TRUE") 
hoặc
User.where(tall: [false, nil])
```

>[benjamin roth's post](https://medium.com/rubyinside/active-records-queries-tricks-2546181a98dd)






