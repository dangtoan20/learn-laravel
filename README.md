# restful-api

# HTTP method theo chuẩn RESTful API:
## GET: Truy xuất tài nguyên (Read)
## POST: Tạo tài nguyên mới (Create)
## PUT/ PATCH: Cập nhật, sửa đổi tài nguyên (Update)
## DELETE: Xoá tài nguyên (Delete)

# Các phương thức trong Controller:
## Method
## index()                          Hiển thị danh sách tài nguyên
## create()                         Thêm mới
## store()                          Lưu trữ tài nguyên mới
## show($id)                        Hiển thị một tài nguyên theo tham số truyển vào
## edit($id)                        Sửa một tài nguyên theo tham số truyển vào
## update(Request $request, $id)    Cập nhật một tài nguyên theo tham số truyển vào
## destroy($id)                     Xoá một tài nguyên theo tham số truyền vào
