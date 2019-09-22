### Magic Comments in Ruby
Có 3 phần trong bài này:
- Comments vs. magic comments
- Specifications
- Existing magic comments

#### Comments vs. Magic Comments
Trong Ruby, bạn có thể chú thích code của mình bằng các comment.

```ruby
# Polylithic linked list structure
class LinkedList
end
```
Ngoài các comment trên, trong ruby còn có 1 loại là magic comment, đây là loại được ruby thông dịch.

```ruby
# encoding: big5

''.encoding # => #<Encoding:Big5>
```
Ở đây, comment `# encoding: big5` được Ruby thông dịch.
Khi Ruby thông dịch đến comment này, nó sẽ tự động đặt mã hóa của bất kỳ chuỗi nào được khai báo trong tệp này thành Mã hóa: Big5

#### Specifications
**Syntaxes**
Có 2 syntax để định nghĩa magic comment
```ruby
# encoding: UTF-8
# -*- encoding: UTF-8 -*-
```

**Multiple files**
Phạm vi của magic comment.
```ruby
# encoding: big5

''.encoding # => #<Encoding:Big5>

require './world.rb'
```

```ruby
word.rb
''.encoding # => #<Encoding:UTF-8>
```
Qua trên ta thấy magic comment khi định nghĩa chỉ có tác dụng ở trong file đó. Các file require không bị ảnh hưởng.

**Multiple magic comments**
Chúng ta có thể định nghĩa nhiều magic comment trong 1 file 
```ruby
# encoding: big5
# frozen_string_literal: true
```

#### Existing magic comments
**The encoding: magic comment**
Trong ruby, mặc định encoding là UTF-8. Sử dụng magic comment trên cho phép chúng ta chuyển encoding string trong file về loại chúng ta mong muốn.
```ruby
# encoding: big5

''.encoding # => #<Encoding:Big5>
```
> Precedence
```ruby
# encoding: big5
# encoding: iso-8859-2
# coding: binary
```
Với file có nhiều hơn 1 định nghĩa magic comment. Thì chỉ magic comment đầu tiên được chấp nhận. Các loại khác sẽ được bỏ qua.

**The frozen_string_literal: magic comment**
```ruby
# frozen_string_literal: true

p 'key'.object_id # => 70306598556120
p 'key'.object_id # => 70306598556120
```
Việc sửa dụng magic comment trên thì chỉ 1 object tạo ra cho 1 chuỗi. Dựa trên nội dung chuỗi.
>Precedence
Với magic comment này thì chỉ magic comment cuối cùng được nhận, các magic comment khác sẽ được bỏ qua.
```ruby
# frozen_string_literal: false
# frozen_string_literal: true

p 'key'.object_id # => 70306598556120
p 'key'.object_id # => 70306598556120
```

**The warn_indent: magic comment**
Magic comment này tương tự command `ruby -w warn_indent.rb`
```ruby
# warn_indent: true

def h
  end # bad indentation
```
>Precedence
Chỉ magic comment cuối cùng được nhận, các comment khác sẽ bị bỏ qua.
```ruby
# warn_indent: false
# warn_indent: true

def h
  end # bad indentation
```




