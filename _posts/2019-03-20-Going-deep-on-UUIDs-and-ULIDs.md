### IDs
Hầu hết các web application đều sử dụng ids increment aumomatically.
```ruby
p1 = Person.create!
p1.id
# => 1

p2 = Person.create!
p2.id
# => 2
```
Database có thể sinh id tuần tự bởi vì nó lưu trữ giá trị bộ đếm tăng lên khi tạo 1 bản ghi mới.
Với dạng sequential ids thì rất dễ implement. Trong trường hợp bài toán nhỏ thì implement cách này rất tốt, tuy nhiên với bài toán lớn hơn, lượng dữ liệu nhiều thì gặp phải các vấn đề sau.
- Không thể tạo bản ghi đồng thời, vì phải đợi id trước được lưu xong thì mới tính toán ra id mới để lưu.
- Việc kiểm tra làm cho tốc độ bị giảm xuống.
- Khó khăn trong việc mở rộng quy mô lưu trữ dữ liệu.
Bên cạnh đó, việc sử dụng ids cũng có thể làm rò rỉ dữ liệu người dùng.
- Có thể đoán được id của người khác.
- Đoán được dịch vụ bạn cung cấp có bao nhiêu user.

### UUID (Universally Unique IDentifier)
`123e4567-e89b-12d3-a456-426655440000`
UUID trông rất khác so với sequential ids. Nó là 1 chuỗi số 128 bit.
UUID được sinh ra bằng cách sử dụng thuật toán [RFC 4122](https://www.ietf.org/rfc/rfc4122.txt)
Chúng ta có đến 5 loại UUID được định nghĩa trong thuật toán trên. Và chia thành 2 dạng sinh UUID.
**Time and randomness-based:**
- Type 4: A randomly-generated id.
- Type 1: The ID contains the host's MAC address and the current timestamp. These are deprecated because they're too easy to guess. (Loại UUID này chưa các yếu tô mang tính cục bộ như MAC address)
- Type 2: These seem to be uncommon. They appear to be purpose-built for an antiquated form of RPC. (Nói chung là trong RFC không quy định rõ nên nhiều cài đặt UUID đã bỏ qua loại này)
**Name based algorithms:**
- Type 5: Uses an SHA-1 hash to generate the UUID. Recommended.
- Type 3: Uses an MD5 hash and is deprecated because MD5 is too insecure.

Lằng nhằng thật sự, mỗi sinh cái UUID mà lắm kiểu, lắm cách.
Trong ruby, ta có thể sinh ra UUID thông qua uuidtools gem. Gem hỗ trợ với các loại UUID, ngoại trừ type 2 ở trên.
```ruby
require "uuidtools"

# Type 1
UUIDTools::UUID.timestamp_create
# => #<UUID:0x2adfdc UUID:64a5189c-25b3-11da-a97b-00c04fd430c8>

# Type 4
UUIDTools::UUID.random_create
# => #<UUID:0x19013a UUID:984265dc-4200-4f02-ae70-fe4f48964159>

# Type 3
UUIDTools::UUID.md5_create(UUIDTools::UUID_DNS_NAMESPACE, "www.widgets.com")
# => #<UUID:0x287576 UUID:3d813cbb-47fb-32ba-91df-831e1593ac29>

# Type 5
UUIDTools::UUID.sha1_create(UUIDTools::UUID_DNS_NAMESPACE, "www.widgets.com")
# => #<UUID:0x2a0116 UUID:21f7f8de-8051-5b89-8680-0195ef798b6a>
```

### ULID (Universally Unique Lexicographically Sortable Identifier)
Để tìm hiểu thêm, bạn có thể vào [đây](https://github.com/ulid/spec)

```
 01AN4Z07BY      79KA1307SR9X4MV3

|----------|    |----------------|
 Timestamp          Randomness
   48bits             80bits
```
   
Cấu trúc của ULID bao gồm 128 bit. 48 bit đầu được sử dụng để lưu trữ timestamp (độ chính xác là milisec), 80 bit sau là random. ULID có các đặc tính, cũng như các ưu điểm sau:
- Cũng sử dụng 128 bit, tương tự UUID, dễ migration.
- 1.21e+24 unique ULIDs per millisecond (quá nhiều)
- Độ dài chuỗi UUID là 26, so với 36 của UUID thì rõ ràng ngắn hơn.
- Chỉ có các ký tự Upcase
- Không có ký tự đặc biệt(URL safe)
- Vì sử dụng 48 bit để lưu timestamp nên có thể sắp xếp.
- Đẹp hơn UUID là cái chắc

