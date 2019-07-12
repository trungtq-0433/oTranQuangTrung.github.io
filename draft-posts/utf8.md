1 string thuộc Unicode Encoding có 4 loại chuẩn hóa.
- NFC
- NFD
- NFKC
- NFKD

Mặc định sẽ là NFC

Tuy nhiên trong tiếng nhật có ký tự whitespace "　" không thể sử dụng hàm strip trong ruby.
Nguyên nhân có thể là do logic hàm strip trong ruby lúc check ký tự whitespace không thể handle được case là whitespace với code 0x3000.
Thông thường space gõ từ bàn phím không xử lý gì đặc biệt thì nó sẽ có code 0x20.

Thế nên trong String ruby có hàm `unicode_normalize` để chuyển đổi string về cùng 1 dạng chuẩn hóa.

`"　".unicode_normalize(:nfkd)`


Encoding: UTF-8, có rất nhiều loại khác nữa.
Unicode Normalize: C

NFKD và NFKC khác nhau ở chỗ followed by Canonical Composition
Có nghĩa là nếu sử dụng chuản followed by Canonical Composition thì sẽ có 1 bước xử lý để đảm bảo string kết quả có "tương đương" với string đầu vào hay không?

```ruby 
 String.new.force_encoding(str.encoding).tap do |res|
 ...
 end
```

Tùy từng hoàn cảnh ta có cách sử dụng cho thích hợp.


Chúng ta hãy theo dõi bảng so sánh sau để hiểu rõ đoạn trên:
http://unicode.org/reports/tr15/

http://unicode.org/reports/tr15/images/UAX15-NormFig3.jpg

http://unicode.org/reports/tr15/images/UAX15-NormFig4.jpg

http://unicode.org/reports/tr15/images/UAX15-NormFig5.jpg

http://unicode.org/reports/tr15/images/UAX15-NormFig6.jpg
