### Overview
Trong dự án của mình có 1 vấn đề liên quan đến việc xử lý ký tự whitespace. 
Điều đầu tiên mình nghĩ đến là `strip` method. 

Test1:
```ruby
"   xxx xxx    ".strip
=> "xxx xxx" 
good
```

Test2:
```ruby
"　xxx".strip
=> "　xxx"
bad
```

Test2 ta có thể thấy hàm strip hoạt động không đúng. Chúng ta cùng tìm hiểu xem sao.

### Khái niệm
** Unicode ** là bảng mã chứa gần như toàn bộ các kí tự của hầu hết các ngôn ngữ trên toàn cầu.
** UTF8 ** là phương thức Encoding rất phổ biến để miêu tả bảng mã Unicode trên bộ nhớ.

Ex1:
```
" "
Name: Space
Unicode number: U+0020
Block: Basic Latin
﻿The Basic Latin Unicode block is the first block of the Unicode standard, and the only block which is encoded in one byte in UTF-8.
```

Ex2:
```
"　"
Name: Ideographic Space
Unicode number: U+3000
Block: CJK Symbols and Punctuation
﻿CJK Symbols and Punctuation is a Unicode block containing symbols and punctuation in the unified Chinese, Japanese and Korean script.
```

Trở lại vấn đề `strip` ban đầu. Tại sao `strip` không hoạt động ở test2?

Theo ý kiến của mình: Nguyên nhân có thể là do logic hàm `strip` trong ruby lúc check ký tự whitespace không thể handle được case whitespace với code U+3000.

Thông thường space gõ từ bàn phím không xử lý gì đặc biệt thì nó sẽ có code U+0020.

Đoạn này mình có xem code của ruby, tuy nhiên nó viết bằng `C` nên mình cũng chẳng hiểu gì, đoán bừa vậy.

### Unicode Normalization Forms là gì?
=> Là các hình thức chuẩn hóa của Unicode text.

Vậy có mấy loại?
=> 4 loại

|Form|Description|
|----------------|-------------------------------|
|Normalization Form D (NFD) |Canonical Decomposition|
|Normalization Form C (NFC)|Canonical Decomposition, followed by Canonical Composition|
|Normalization Form KD (NFKD)|Compatibility Decomposition|
|Normalization Form KC (NFKC)|Compatibility Decomposition, followed by Canonical Composition|

- Có 2 forms chuẩn hóa convert to composite characters: Normalization Form C and Normalization Form KC. Khác nhau ở chỗ followed by Canonical Composition Có nghĩa là nếu sử dụng chuản followed by Canonical Composition thì sẽ có 1 bước xử lý để đảm bảo string kết quả có "tương đương" với string đầu vào hay không?

- Trong NFKC và NFKD chứ K được sử dụng để biểu thị tính tương đương, tránh confick với C trong Composition.

Để hiểu hơn chúng ta xem các ví dụ sau:

**Singletons**

![[Singletons](http://unicode.org/reports/tr15/#Singletons_Figure)](http://unicode.org/reports/tr15/images/UAX15-NormFig3.jpg)


**Canonical Composites**

![](http://unicode.org/reports/tr15/images/UAX15-NormFig4.jpg)


**Compatibility Composites**

![](http://unicode.org/reports/tr15/images/UAX15-NormFig6.jpg)


Qua kiến thức về normalize nói trên, vậy vấn đề `strip` ban đầu xử lý như thế nào?

Trong `String` ruby có 1 hàm là `unicode_normalize`. Được sử dụng để convert 1 string sang normalize khác.

Sử dụng `unicode_normalize` như thế nào?
```ruby
"　xxx".unicode_normalize(:nfkc).strip
=> "xxx"
```

### Extend
Ngoài ra chúng ta cũng có thể sử dụng regex để giải quyết bài toán này.
```ruby
"　xxx".gsub(/[[:space:]]|/, "")
=> "xxx"
```
