CHMOD chính là thao tác thay đổi các quyền sau:
"Read" (Đọc): Viết tắt là "r", và được biểu diễn bằng số 4
"Write" (Ghi / Chỉnh sửa): Viết tắt là "w", và được biểu diễn bằng số 2
"Execute" (Thực thi): Viết tắt là "x", và được biểu diễn bằng số 1 

CHMOD cùng lúc thay đổi quyền hạn trên các File/thư mục với các đối tượng sau:
"Owner": Chủ sở hữu của File/thư mục,
"Group": Nhóm mà Owner là thành viên,
"Public / Others/ Everybody": Những người còn lại.

Ví dụ CHMOD 755 có nghĩa là
7 = 4 + 2 + 1: Người sở hữu thư mục có quyền đọc thư mục (read); chỉnh sửa thư mục (write); liệt kê các thư mục và file bên trong (execute);
5 = 4 + 0 + 1: Những người cùng nhóm chỉ có quyền đọc thư mục (read); liệt kê các thư mục và file bên trong (execute)
5 = 4 + 0 + 1: Những người còn lại chỉ có quyền đọc thư mục (read); liệt kê các thư mục và file bên trong (execute)

