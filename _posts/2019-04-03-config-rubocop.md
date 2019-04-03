Các trường hợp dưới đây, ít nhiều các bạn làm dự án công ty đã gặp.
- Cùng 1 logic, chỗ thì viết thế này, chỗ viết thế kia.
- Trong ruby có nhiều method alias, trong project chỗ dùng method này, chỗ dùng method alias.
- Bạn làm reviewer, bạn quá chán việc check các convention. Vì bản chất cũng không gây ra lỗi gì mà comment thì lại mất thời gian. Để thời gian ngồi review logic thì hợp lý hơn.
- Có một số method performance hơn, tuy nhiên lại không được sử dụng.
- ... 

Giải pháp đặt ra là dùng các tool tự động check những vấn đề trên. 1 trong các tool đó ở cty mình là rubocop. Tuy nhiên file config của cty mình thấy cũng đã outdate. Hoặc 1 số config lỗi thời, chưa được cập nhật, hoặc 1 số config mình thấy hay ho tuy nhiên chưa được thêm vào.
Thế nên mình ngồi tạo lại file config cho phù hợp với mình. Các bạn muốn sử dụng có thể tham khảo ở dưới đây.
[.rubocop.yml](https://gist.github.com/oTranQuangTrung/8703a46ac3d83120f781a298322d4072)
[.rubocop_enabled.yml](https://gist.github.com/oTranQuangTrung/12bbac5bd51f8afe99574ee26e31f11a)
[.rubocop_disabled.yml](https://gist.github.com/oTranQuangTrung/0bc417562b8acc1d02503a317231f730)
