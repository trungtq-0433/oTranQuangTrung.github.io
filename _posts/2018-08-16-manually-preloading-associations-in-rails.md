---
layout: post
title:  "Manually preloading associations in rails"
categories: RoR
author: tqtrung09
---

* content
{:toc}

Chúng ta có lẽ đã quen với việc sử dụng `preloading` trong rails thông qua việc sử dụng `includes`, `eager_load`, `preload`
trực tiếp trên `activerecord` nhằm phục vụ cho mục đích tăng performance cho hệ thống.

Tuy nhiên trong một vài trường hợp hiếm khi xảy ra, chúng ta có 1 tập hợp gồm các bản ghi thuộc nhiều class khác nhau và mỗi class có các preloading khác nhau. Trong trường hợp này chúng ta cần sử dụng đến manually preload associations.






Chúng ta cùng xem ví dụ dưới đây.

```ruby
class Product
 belongs_to :item, polymorphic: true, inverse_of: :product
end
```

```ruby
class CreditCard
  has_one :product, as: item, inverse_of: :item, dependent: :destroy
  has_many :xxx
end

class DebitCard
  has_one :product, as: item, inverse_of: :item, dependent: :destroy
  has_many :yyy
end


class BitcoinExchange
  has_one :product, as: item, inverse_of: :item, dependent: :destroy
  has_many :zzz
end

```

Với kiến trúc như trên chúng ta có 1 bài toán là lấy toàn bộ record của bảng `product` nhằm mục đích hiển thị các thông
tin liên quan đối với mỗi `CreditCard` `DebitCard` `BitcoinExchange` , tuy nhiên với mỗi loại chúng ta có preloading
khác nhau tương ứng là `xxx` `yyy` `zzz`.

Ở đây chúng ta sẽ overider lại class `ActiveRecord::Associations::Preloader`

```ruby
class ProductPreloader < ActiveRecord::Associations::Preloader
  # Định nghĩa các preload associations
  CREDIT_CARD_PRELOAD_ASSOCIATIONS = [:xxx].freeze
  DEBIT_CARD_PRELOAD_ASSOCIATIONS = [:yyy].freeze
  BITCOIN_EXCHANGE_PRELOAD_ASSOCIATIONS = [:zzz].freeze

  def preload records, associations = nil, preload_scope = nil
    super
    super records.select{|r| r.type == CreditCard.name}, CREDIT_CARD_PRELOAD_ASSOCIATIONS, preload_scope
    super records.select{|r| r.type == DebitCard.name}, DEBIT_CARD_PRELOAD_ASSOCIATIONS, preload_scope
    super records.select{|r| r.type == BitcoinExchange.name}, BITCOIN_EXCHANGE_PRELOAD_ASSOCIATIONS, preload_scope
  end
end

```

Như vậy là chúng ta đã hoàn thành việc manual preloading cho từng lọai record trong tập các record.
