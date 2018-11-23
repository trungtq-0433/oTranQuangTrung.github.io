---
layout: post
title:  "Rails notes"
categories: javascript
author: tqtrung09
---

* content
{:toc}

Dưới đây là tổng hợp một số note mình tìm hiểu thấy hay về ngôn ngữ ruby và framework rails.






### `Extend object`
Được hiểu đây là cách thêm code từ một Module khác.

```ruby
module Ispeak
  def says
    "greetings aliens!"
  end
end

module Ieat
  def eats
    "spinach"
  end
end

module Inhabitant
  def says
    "I'm strong to the finish"
  end
end

class Human
  extend Ispeak # add class methods from Ispeak
  include Inhabitant # add instance methods from Inhabitant
end

Human.extend Ieat # add class methods from Ieat

puts Human.says # -> greetings aliens!
puts Human.eats # -> spinach

popeye = Human.new

puts popeye.says # -> I'm strong to the finish
```

Sau đây là một ứng dụng về validate model trong case user registration.
Chúng ta không muốn viết vào model vì nó còn vướng tới contex create, update. Và trong ngữ cảnh được tạo ra từ FactoryBot thì cũng không muốn nằm trong phạm vi.

```ruby
# app/models/user/registration_context.rb
module User::RegistrationContext
  def self.extended(model)
    class << model
      validates :terms_of_service_accepted, acceptance: true
    end
  end

  attr_accessor :terms_of_service_accepted
end

user = User.new
user.extend(User::RegistrationContext)
user.terms_of_service_accepted = "0"
user.valid?
=> false
user.errors.messages[:terms_of_service_accepted]
=> ["must be accepted"]

```












---
layout: post
title:  "Self study javascript, ES6, React P2"
categories: javascript
author: tqtrung09
---

* content
{:toc}
Tiếp nối phần 1 về ES6, phần này chúng ta sẽ tìm hiểu về ReactJS. Những thứ được mình nêu trong bài là những phần mình thấy cần đc chú ý nhất.








### `Conditional rendering`

Trong 1 function thì chỉ có duy nhất 1 hàm return, không tránh khỏi nhiều lúc chúng ta cần điều kiện khi render. Dưới đây là một số casestudy tiêu biểu.

```js
class ConditionalRendering extends React.Component {

  caseStudy1() {
    if(false) {
      return "false"
    }
    return "false"
  }

  caseStudy2() {
    let button ;
    if(true){
      button = <Button>submit</Button>
    } else {
      button = <Button>cancel</Button>
    }

    return (
      <div>
        {button}
      </div>
    )
  }

  caseStudy3() {
    // true && expression always evaluates to expression, and false && expression always evaluates to false.
    return (
      { true &&
        <p>
          true
        </p>
      }
    )
  }

  caseStudy4() {
    return (
      <div>
        {true ? (<Button>TRUE</Button>) : 'FALSE'}
      </div>
    )
  }
}
```

### `List and Keys`

In React, transforming arrays into lists of elements is nearly identical.

```js
function NumberList(props) {
  const numbers = [1, 2, 3, 4, 5];
  const listItems = numbers.map((number) =>
    <li>{number}</li>
  );
  return (
    <ul>{listItems}</ul>
  );
}
// Khi chạy code trên sẽ nhận được cảnh báo là cần thêm key cho list item

// Key được react sử dụng để xác định item nào change, item nào thêm mới hoặc remove.
const todoItems = todos.map((todo, index) =>
  // Only do this if items have no stable IDs
  <li key={index}>
    {todo.text}
  </li>
);

// Keys Must Only Be Unique Among Siblings
```



















