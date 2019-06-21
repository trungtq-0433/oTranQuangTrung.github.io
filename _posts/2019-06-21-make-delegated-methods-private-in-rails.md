Sử dụng `delegate` method từ ngày biết đến rails.

Cú pháp đơn giản: `delegate :first_name, :last_name, to: :@user`

Tuy nhiên một vấn đề xảy ra là nhiều khi việc delegate đang làm cho mất tính bao đóng của OOP. Do các method sau khi delegate là public.

```ruby
class UserDecorator
  def initialize(user)
    @user = user
  end

  def full_name
    "#{first_name} #{last_name}"
  end

  private

  delegate :first_name, :last_name, to: :@user
end
```
Với đoạn code trên nếu chúng ta không để delegate vào private thì dĩ nhiên là có sự thừa thãi ở đây.

Tuy nhiên đưa delegate vào private như trên có đang hoạt động đúng?
```ruby
user = User.new(first_name: 'John', last_name: 'Doe')
decorated_user = UserDecorator.new(user)

decorated_user.full_name #=> John Doe
decorated_user.first_name #=> John
decorated_user.last_name #=> Doe
```
Đưa vào private mà vẫn gọi được?

Việc đặt delegate vào dưới private scope không có tác dụng, bởi vì delegate không hiểu để đưa method vào private scope.

Vậy phải làm sao?
```ruby
class UserDecorator
  # def initialize...

  delegate :first_name, :last_name, to: :@user
  private :first_name, :last_name
end
```
Cách trên giải quyết được câu hỏi, tuy nhiên ta thấy nó kiểu không DRY lắm.

Bởi vì delegate trả về 1 danh sách các method nên chúng ta có thể sử dụng cách sau.
```ruby
class UserDecorator
  # def initialize...

  private *delegate(:first_name, :last_name, to: :@user)
end
```
Ở Rails 6, chúng ta có thể viết như sau.
```ruby
# Rails 6+
delegate :first_name, :last_name, to: :@user, private: true
```
Link pr: https://github.com/rails/rails/pull/31944

Reference: https://nithinbekal.com/posts/rails-delegate/


