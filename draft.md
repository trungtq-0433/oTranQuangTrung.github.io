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


=================

Gem Whenever
Đối với việc sử dụng rbenv thì cần set như dưới để chạy đc trên development

env :PATH, ENV['PATH']
env :GEM_PATH, ENV['GEM_PATH']
env :GEM_HOME, ENV['GEM_HOME']


=================
# memoize db/memcache results in instance variable dynamically
def memoize_results(key)
  return instance_variable_get(key) if instance_variable_defined?(key)
  instance_variable_set key, yield
end

# usage
MY_CONSTANT = [:active, :inactive]
MY_CONSTANT.each { |key|
  define_method("#{key}_users") do
    memoize_results("@#{key}_users") do
      User.send(key).all # assumes that user responds to active, inactive(via scope/filter etc..)
    end
  end
}




==================

https://ieftimov.com/variable-hoisting-ruby
https://gist.github.com/oTranQuangTrung/e8e2b9ef27df08e045dbafdf4d7cdeba?fbclid=IwAR0YvCeCDtHW-fz0RAiIJtxuVSxRUWBVW2Ỉ8D8KM1qVz41ZLh5_oMK_quo

========================================================

1.
val1 = true and false  # hint: output of this statement in IRB is NOT value of val1!
val2 = true && false

and and or  operator have lower precedence  than =
whereas, && and  || higher precedence than =

(val1 = true) and false    # results in val1 being equal to true
val2 = (true && false)     # results in val2 being equal to false

2.
What is the difference between calling super and calling super()?
A call to super invokes the parent method with the same arguments that were passed to the child method. An error will therefore occur if the arguments passed to the child method don’t match what the parent is expecting.

A call to super() invokes the parent method without any arguments, as presumably expected. As always, being explicit in your code is a good thing.

3.
== – Checks if the value of two operands are equal (often overridden to provide a class-specific definition of equality).

=== – Specifically used to test equality within the when clause of a case statement (also often overridden to provide meaningful class-specific semantics in case statements).

eql? – Checks if the value and type of two operands are the same (as opposed to the == operator which compares values but ignores types). For example, 1 == 1.0 evaluates to true, whereas 1.eql?(1.0) evaluates to false.

equal? – Compares the identity of two objects; i.e., returns true iff both operands have the same object id (i.e., if they both refer to the same object). Note that this will return false when comparing two identical copies of the same object.

4.

include: mixes in specified module methods as instance methods in the target class
extend: mixes in specified module methods as class methods in the target class

5.
Array#each method iterates over the elements of the array and executes the provided block each time. However, it returns the original array unaffected.
Array#map will return a new array of elements containing the values returned by the block it is provided. It also does not affect the original array

upcased = ["one", "two", "three"].map {|n| puts n.upcase } ??


6.
The :: is a unary operator that allows: constants, instance methods and class methods defined within a class or module, to be accessed from anywhere outside the class or module.
Remember: in Ruby, classes and methods may be considered constants too.
```ruby
MR_COUNT = 0        # constant defined on main Object class
module Foo
  MR_COUNT = 0
  ::MR_COUNT = 1    # set global count to 1
  MR_COUNT = 2      # set local count to 2
end
puts MR_COUNT       # this is the global constant
puts Foo::MR_COUNT  # this is the local "Foo" constant
```

7.

```ruby
# wants a proc, a lambda, AND a block
def three_ways(proc, lambda, &block)
  proc.call
  lambda.call
  yield # like block.call
  puts "#{proc.inspect} #{lambda.inspect} #{block.inspect}"
end

anonymous = Proc.new { puts "I'm a Proc for sure." }
nameless  = lambda { puts "But what about me?" }

three_ways(anonymous, nameless) do
  puts "I'm a block, but could it be???"
end
 #=> I'm a Proc for sure.
 #=> But what about me?
 #=> I'm a block, but could it be???
 #=> #<Proc:0x00089d64> #<Proc:0x00089c74> #<Proc:0x00089b34>
 ```

 - Lambda check argument | Proc is not
 - 1 method, return in lambda then continue run to the end method.
 - 1 method, return in proc then end method.

 Everything method default receive {}

8.
It’s dangerous to convert user supplied parameters to symbols, since Symbol objects in Ruby are not garbage collected. An attacker could send a series of requests with random keys that would be turned into symbols, quickly exhausting your server’s available memory and taking down your site.
Using slice or using string insted symbol

9.
TEST:
- Tim bug
- design code
- Phat hien diem yeu cau truc code
=> test setup nhieu va phuc tap -> code thuc hien qua nhieu viec
=> Code ko tot -> test kho.

1 object can test:
- incomming message: pubic method object
- outgoing message: Nhung ham public ma object khac goi toi.

Outgoing messages gồm 2 loại:
- Query messages: là những method không có hiệu ứng phụ (ghi file, ghi vào DB, gọi api bên ngoài ...), những method này đơn thuần chủ trả về giá trị và không thực hiện những nhiệm vụ đặc biệt.

- Command messawafages: là những method có hiệu ứng phụ. Sending object sẽ phải chịu trách nhiệm test việc gọi những method này. Sending object cần chứng minh là những message này được gọi một cách chính xác. Những test như thế này được gọi là test behavior (test hành vi). Test behavior thường bao gồm việc test xem message được gọi bao nhiêu lần, với những tham số gì thì message mới được gửi đúng.

10.
Mock & Stub?
A stub is only a method with a canned response, it doesn’t care about behavior.
=> allow(stub).to receive(:flip).and_return("ruby-flipped.jpg")
A mock expects methods to be called, if they are not called the test will fail
```ruby
it "calls the flip method with the correct arguments" do
  mock = double("mini_magick")
  expect(mock).to receive(:flip).with("ruby.jpg").and_return("ruby-flipped.jpg")
  img = ImageFlipper.new(mock)
  img.flip("ruby.jpg")
end

```

11.
1970-01-01 => Unix time start


12.
Date in mysql UTC+0. User.where(updated_at: Date.current.beginning_of_day..Date.current.end_of_day)

13.
where.not message_converted: true   => get false
where.message_converted: [nil, false]  => get false and null

14.
Assest file: image-url

15.
public, protect, private
protect duoc goi tu cac class ke thua bang self.method


16. Binary search
- Mang phai duoc sap xep
- Tim midindex = (end + start) / 2
Check gia tri can tim o phia ben nao, co bang midindex ko?
- startIndex = midIndex + 1 và midIndex = (endIndex + startIndex) / 2

```ruby
a = [1,2,3,4,5,6,7,8]
find = 7

low = 0
high = a.size
while low < high
mid = (low + high)/2
  if a[mid].eql? find
    return mid
  elsif a[mid] > find
    last = mid - 1
  else
    first = mid +1
  end
end
```

17. Bubble Sort
a = [2,9,5,7,1,8,4,5]

length = a.size

(length - 1).time do |i|
  (length - 1 - i).time do |j|
    if a[j] > a[i]
    tmp = a[j]
    a[i] = a[j]
    a[j] = tmp
    end
  end
end

18. Polymorphis
user -> image
product -> image

image: id, imageable_id, imageable_type
belongs_to :imageable, polymorphic: true
has_many :images, as: :imageable

19. Single Table Inheritance
De tuy bien hon
must :type attributes

20. optimistis & permistis
optimistis: :lock_version se tang len 1 khi co update instance. data type phải là integer và có default: 0
permistis: .lock! de khoa instance, check bang viec instance da duoc save hay chua?

21. A.includes(:xxx)
Su dung 2 query, 1 query A. 2 query xxx voi dk A.id
joins: 1 query

22. ACID
Trong 1 transaction
Atomic: tat ca cac action cung success or fail.
Consistency: Sau khi transaction, DB phai duoc giu dc tinh nhat quan, ko co chuyen truong integer luu gia tri text
Isolate: 1 transaction nam trong 1 moi truong doc lap, khong co chuyen 2 transaction cung tac dong len 1 data
Durable: Dam bao backup sau khi xay ra su co.

23. SOLID
single responsibility: 1 class chi chua 1 chuc nang
open-close: Mo rong class, khong modify class. (extend, include)
liskov: các object của class con có thể thay thế class cha mà không làm thay đổi tính đúng đắn của chương trình.
  1 class cha tên Vịt. Các class VịtBầu, VịtXiêm có thể kế thừa class này, chương trình chạy bình thường. Tuy nhiên nếu ta viết class VịtChạyPin, cần pin mới chạy được. Khi class này kế thừa class Vịt, vì không có pin không chạy được, sẽ gây lỗi. Đó là 1 trường hợp vi phạm nguyên lý này.


interface segregation: Chia nho interface
dependency inversion: cac module nen phu thuoc vao abstractions
  interface chính là đuôi tròn, implementation là bóng đèn tròn và bóng đèn huỳnh quang. Ta có thể swap dễ dàng giữa 2 loại bóng vì ổ điện chỉ quan tâm tới interface (đuôi tròn), không quan tâm tới implementation.

24. DI (dependency injection)
DI: priciples
IoC: pattern (delegate)
DI: Cach thuc hien

- constructor inject: cac dependency inject thong qua constructor class. (thong dung)
- Setter Injection
- Interface Injection (it sd)

xxx = XXX.new(YYY.new, ZZZ.new)
