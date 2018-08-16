---
layout: post
title:  "How to avoid !important in css"
categories: Frontend
author: tqtrung09
---

* content
{:toc}


`!important`: Đây là từ khóa không lạ lẫm gì đối với 1 backend-developer.
Bạn đã có bao giờ gặp phải trường hợp cố gắng style cho một hoặc nhiều html element mà nó không có tác dụng?

Có vẻ như trang web đang bỏ qua style của bạn mà bạn không tìm được nguyên nhân?

Và bạn đã sử dụng `!important`, cú pháp theo mình gọi thì nó là huyền thoại của các backend-developer khi cố gắng thiết kế giao
diện.





## `Nerver use !important`
`!important` được các backend-developer sử dụng rất nhiều. Tuy nhiên, việc sử dụng `!important` sẽ bị coi là không thế
chấp nhận được. Bởi vì:
 - Quá trình maintain về sau gặp khó khăn.
 - Làm hỏng nguyên tắc cốt lõi của css đó là specificity.
Vì vậy chúng ta cần phải loại bỏ suy nghĩ sử dụng `!important!`.

Như bạn thấy, không sử dụng `!important` thì chúng ta phải làm như sao để đạt được kết quả tương đương như sử dụng `!important`?

## `Specificity Calculations`

```html
  <div id="sidebar">
    <p class="bio">text here</p>
  </div>
```

Chúng ta cùng xem các ví dụ sau đây ứng với đoạn html trên.

Ví dụ 1:
```css
  p {font-size: 12px}
  p.bio {font-size: 14px}
```
Với đoạn css trên, chúng ta có thể phán đoán được giá trị font-size của thẻ p là 14px, bởi vì `p.bio` specific hơn `p`.

Tuy nhiên, trong một số trường hợp khác, `specificity` không dễ dàng xác định như ví dụ trong trường hợp sau.

Ví dụ 2:

```css
  div p.bio {font-size: 14px}
  #sidebar p {font-size: 12px}
```

Thọat nhìn, ta sẽ nghĩ `div p.bio` sẽ rõ ràng, vì nó được mô tả cụ thể. 

Tuy nhiên thực tế thì `#sidebar p` mới là đoạn code sẽ ảnh hưởng đến `font-size` của thẻ `p`. 

Để trả lời cho vấn đề trên, chúng ta cần xem lại quy tắc tính `specificity` trong css.

`Specificity` được tính toán dựa trên việc đếm các thành phần css khác nhau của element, và biểu diễn chúng dưới dạng
(a, b, c, d).

```
  * Element, Pseudo Element: d = 1 – (0,0,0,1)
  * Class, Pseudo class, Attribute: c = 1 – (0,0,1,0)
  * Id: b = 1 – (0,1,0,0)
  * Inline Style: a = 1 – (1,0,0,0)
```
Theo bảng quy chiếu trên ta có thể thấy `inline style` specific hơn `id` và `id` specific hơn là `class`.

Áp dụng chúng ta tính toán specificity bằng cách đếm từng thành phần `class`, `id`, `element` ... ở bảng trên và +1 vào
các vị trí a, b, c, d tương ứng.

Và chúng ta hãy nhớ cách so sánh rằng (0,0,1,0) luôn specific hơn (0,0,0,15).

Chúng ta sẽ hình dung dễ hơn thông qua bảng tính mẫu sau:

```css
  p: 1 element – (0,0,0,1)
  div: 1 element – (0,0,0,1)
  #sidebar: 1 id – (0,1,0,0)
  div#sidebar: 1 element, 1 id – (0,1,0,1)
  div#sidebar p: 2 elements, 1 id – (0,1,0,2)
  div#sidebar p.bio: 2 elements, 1 class, 1 id – (0,1,1,2)
```

Áp dụng vào ví dụ ban đầu của chúng ta.
```css
  div p.bio {font-size: 14px} - (0,0,1,2)
  #sidebar p {font-size: 12px} - (0,1,0,1)
```
Specific của `#sidebar p` lớn hơn `div p.bio` thế nên `font-size` sẽ là 12px.

Tuy nhiên nếu ta sử dụng như sau, thì bất chấp độ specific ở dòng thứ 2 mô tả như nào thì cũng không thể vượt qua đc
`!important`. 

```css
  div p.bio {font-size: 14px !important}
  #sidebar p {font-size: 12px}
```

Bây giờ thì chúng ta đã hiểu về `!important`, và hãy xóa nó ra khỏi đầu của bạn khi viết css.

