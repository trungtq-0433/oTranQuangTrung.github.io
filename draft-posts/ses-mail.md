Gặp lỗi:
Sidekiq(mailers) in 3.64ms: SocketError (getaddrinfo: Servname not supported for ai_socktype):
SocketError (getaddrinfo: Name or service not known

Cách điều tra:
Đầu tiên thử gửi mail ở trong rails console. 
Nếu không được với hiện trạng là đã soạn xong email.
```
PCFET0NUWVBFIGh0bWw+DQo8aHRtbD4NCiAgPGhlYWQ+DQogICAgPG1ldGEg
Ao44Oh44O844Or44Ki44OJ44Os44K5KTwvYT4NCiAgPC9ib2R5Pg0K
PC9odG1sPg0K

I, [2019-09-27T19:59:30.817816 #11242]  INFO -- : [ActiveJob] [ActionMailer::DeliveryJob] [003c9db2-db59-4a42-877e-928a8692b992] Performed ActionMailer::DeliveryJob (Job ID: 003c9db2-db59-4a42-877e-928a8692b992) from Sidekiq(mailers) in 2554.53ms
```
Tuy nhiên nhận được 1 trong 2 lỗi trên.

- Lấy thông tin ses mail về development thử.
Nếu OK thì nguyên nhân là do môi trường.

Vậy ta cần xem lại dịch vụ bên AWS. Cụ thể ở đây là đã soạn được mail xong, gửi đi rồi, tuy nhiên đợi respond lại không có.
Thì cần xem ACLs đã open port 587 hay chưa?
Đây là bạn infra bên mình trả lời
Em thêm port cho acls anh ạ
nó liên quan đến đường đi của port trong aws
hơi loằng ngoằng 1 tí

- Sau khi config lại AWS xong thì check lại xem đã gửi được email chưa.
Nếu vẫn bị lỗi trên thì quay lại thử ở trên rails c.
Nếu trên rails C được thì cần remove lại toàn bộ folder current và deploy lại từ đầu.
