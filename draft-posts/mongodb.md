Nosql:
  - None-Relational SQL
  - Phát triển trên Javascript Framework với kiểu dữ liệu là JSON và dạng dữ liệu theo kiểu key và value.
  - Tốc độ, tính năng, khả năng mở rộng.
  - Có thể mở rộng dữ liệu mà không lo tới những việc như tạo khóa ngoại, khóa chính, kiểm tra ràng buộc.
  - Bỏ qua tính toàn vẹn của dữ liệu và transaction để đổi lấy hiệu suất nhanh và khả năng mở rộng
  - Casandar, bigtable


MongoDB:
  - Open source
  - Hướng tài liệu, kiểu json.
  - Thông tin liên quan được lưu trữ cùng nhau để truy cập nhanh.

  SQL Terms/Concepts                  |            MongoDB Terms/Concepts
  database                            |            database
  table                               |            collection
  row                                 |            document or BSON document
  column                              |            field
  index                               |            index
  table joins                         |            embedded documents and linking
  primary key.
  Specify any unique column or column combination as primary key.   |    primary key. In MongoDB, the primary key is automatically set to the _id field.


MongoDB commands:
  - CREATE DATABASE       |   use xxx;
  - CREATE TABLE          |   db.createCollection('students');
  - INSERT RECORD         |   db.students.insert({ name:'thanh', gender: 'male'});
  - UPDATE RECORD         |   db.students.update({ _id: 1 },{$set:{ name: 'thanh update' }});
  - DELETE RECORD         |   db.students.remove({ _id: 1});
  - SELECT ALL            |   db.students.find({});
  - SELECT                |   db.students.find({ name: 'thanh' });


Strong:
  - Mỗi collection, document sẽ khác nhau, linh hoạt trong việc lưu trữ dữ liệu, insert thoải mái.
  - Ko có quan hệ, nên xóa nhanh.
  - id luôn được đánh index
  - Khi có 1 truy vấn, dữ liệu đc cache trên ram, phục vụ cho lần sau mà ko phải đọc từ ổ cứng.

Weekness:
  - Vì ko có quan hệ ràng buộc nên khi thao tác với dữ liệu cần cẩn thận.
  - Collection khác nhau về value, còn key có thể trung lặp
  - Dư thừa dữ liệu
  -
