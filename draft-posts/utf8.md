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
