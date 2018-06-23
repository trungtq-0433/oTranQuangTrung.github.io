---
layout: post
title:  "Regular Expression"
categories: Common
author: tqtrung09
---

* content
{:toc}

`RegEx` hay còn gọi `Regular Expression` là khái niệm khá quen thuộc với lập trình viên. Hay còn gọi là `Biểu thức chính quy`.
`Regex` là một chuỗi ký tự có quy tắc đặc biệt, được dùng làm mẫu (pattern) để phân tích sự trùng khớp của một tập hợp
chuỗi nào đó.
Cá nhân mình thấy, có khá nhiều lập trình viên đi làm 1-2 năm vẫn còn rất mơ hồ về `regex`. Khi có vấn đề thì google
test thử thấy chạy đúng thì copy, paste mà không hiểu bản chất nó xử lý thế nào. Cá nhân mình cũng từng như vậy thế nên
mình đã hạ quyết tâm học hành cẩn thận kỹ thuật này.




## `Regex in JS`
Regex được hỗ trợ bởi hầu hết các ngôn ngữ lập trình và tương ứng với mỗi ngôn ngữ thì cú pháp của regex có khác nhau. Tuy nhiên cơ bản là giống nhau, những phần khác nhau không nhiều. Ở bài viết này mình lựa chọn regex trong JS để học.

Trong JS thì `Regex` là một chuỗi tuy nhiên không được bọc bởi cặp `"`, hoặc `'` mà được bọc bởi cặp `/`.

## `Cú pháp`
`/pattern/modifiers`

`pattern`: là một chuỗi có quy tắc, quy tắc như nào mình sẽ đề cập ở dưới.

`modifiers`: là cấu hình cho chuỗi pattern.
  - `i`:  So sánh không phân biệt chữ hoa chữ thường (case-insensitive) 
  - `g`:  So khợp toàn bộ chuỗi cần tìm 
  - `m`:  So khớp luôn cả các dữ liệu xuống dòng (multiline) 

```js
var regex = /xxx/igm;

pattern: xxx
modifiers: igm
```

## `Quy tắc pattern`
Mình sẽ chia các quy tắc theo các loại và giải thích như sau. Cái nào khó hình dung thì mình sẽ có thêm ví dụ ngay dưới.
Khó dễ là ý kiến chủ quan của mình.

#### `Brackets`

```
[abc]     Tìm các ký tự a, b hoặc c
[^abc]    Tìm các ký tự không phải a, b và c
[0-9]     Tìm các ký tự là chữ số từ 0-9
[a-z]     Tìm các ký tự là chữ cái từ a-z
[^a-z]    Tìm các ký tự không phải là chữ cái từ a-z
[a-zA-Z]  Tìm các ký tự chữ cái từ a-z hoặc A-Z
[^0-9]    Tìm các ký tự không phải chữ số từ 0-9
(x|y)     Tìm ký tự x hoặc y

```

#### `Metacharacters`
```
.         Tìm ký tự bất kì
\w        Tìm ký tự trong bảng chữ cái và chữ số(các ký tự đặc biệt không nằm trong phạm vi này)
\W        Tìm các ký tự không phải là trong bảng chữ cái và chữ số
\d        Tìm ký tự là chữ số
\D        Tìm ký tự không phải là chữ số
\s        Tìm ký tự là khoảng trắng
\S        Tìm ký tự không phải khoảng trắng
\b        Tìm so khớp bắt đầu hoặc kết thúc chuỗi
\B        Tìm so khớp không phải bắt đầu hoặc kết thúc chuỗi
\n        Tìm ký tự xuống hàng
\t        Tìm ký tự tab   
```

#### `Quantifiers`

```
+         Kiểm tra ký tự xuất hiện một hoặc nhiều lần(Đặt sau ký tự muốn kiểm tra)
*         Kiểm tra ký tự xuất hiện không hoặc nhiều lần
?         Kiểm tra ký tự xuất hiện không hoặc một lần
{X}       Kiểm tra ký tự xuất hiện đúng X lần liên tiếp
  => x{2}
{X,Y}     Kiểm tra ký tự xuất hiện tối thiểu X lần và tối đa Y lần liên tiếp
  => x{1,2}
{X,}      Kiểm tra ký tự xuất hiện ít nhất X lần liên tiếp
  => x{3,}
^         Kiểm tra ký tự bắt đầu chuỗi (đặt trước ký tự cần kiểm tra)
  => ^x 
$         Kiểm tra ký tự kết thúc chuỗi (đặt sau ký tự cần kiểm tra))
  => x$
```

Với cú pháp và quy tắc ở trên, chỉ cần dành thời gian ra để thực hành thường xuyên thì mình có thể sử dụng `regex` thành
thục. Tất nhiên là ở mức độ trung bình thôi, đến master thì đòi hỏi sự am hiểu sâu và vận dụng đúng chỗ đúng quy tắc của
`regex`.

## `Practices`
Tiếp theo chúng ta luyện tập 1 số ví dụ cơ bản.

**VD1:** Kiểm tra 1 chuỗi là số.

Pattern số là `\d` hoặc [0-9]

1 chuỗi thì có nghĩa Quantifier là `+`: Xuất hiện 1 hoặc nhiều lần.

Đảm bảo cả chuỗi đó là số thì chuỗi đó bắt đầu và kết thúc đều là chuỗi số `^`, `$`

Từ các dữ kiện trên ta đưa ra đc regex như sau: `^[0-9]+$` hoặc `^\d+$`

**VD2:** Kiểm tra 1 chuỗi là số có 8 chữ số.

Tương tự pattern số là `\d` hoặc `[0-9]`

Có 8 chữ số có nghĩa là số các số trong chuỗi xuất hiện đúng 8 lần `{}`.

Đảm bảo cả chuỗi đó là số thì chuỗi đó bắt đầu và kết thúc đều là chuỗi số `^`, `$`

Regex: `^\d{8}$` hoặc `^[0-9]{8}$`

**VD3:** Kiểm tra 1 chuỗi là số điện thoại di động.

Số di động bắt đầu bằng 0 `^0`

1 chuỗi dài 9-10 ký tự số bất kỳ tiếp theo `\d{9,10}`

Và kết thúc là 1 số. `$`

Regex: `^0\d{9,10}$`

## `References`
>[Freetuts](https://freetuts.net/bang-bieu-thuc-regular-expression-trong-javascript-418.html)

>[Regex101](https://regex101.com/)





