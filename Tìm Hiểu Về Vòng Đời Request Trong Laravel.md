Vòng đời request trong Laravel là gì?
Vòng đời request trong Laravel là chu trình của 1 framework hoạt động từ lúc bắt đầu đến lúc kết thúc. Khi người dùng gửi 1 request, dòng lệnh này sẽ chuyển đến file index.php để xử lý. Tất cả các request gửi lên đều phải đi qua file index.php. Tất cả request được điều hướng đến file đó theo cấu hình web server của bạn. File index.php thường không chứa nhiều code. Nó là nơi bắt đầu để truy cập các phần còn lại của framework.  File index.php sẽ cập nhật các định nghĩa từ composer generated autoloader. Sau đó, nó sẽ lấy instance application Laravel từ file bootstrap/app.php. Nó sẽ tiếp tục thực hiện tiến trình này để tạo ra 1 instance của application/service container. 

 

HTTP và Console Kernels
Request sẽ được gửi đến HTTP kernel hoặc console kernel tùy thuộc vào request đến thuộc loại nào. Hai kernel có vai trò là trung tâm của các request là HTTP và Console Kernels. Các request khác muốn đi qua thì phải thông qua 2 kernel đó. Vì mới làm quen với request của Laravel, bạn chỉ cần tập trung vào HTTP kernel đang lưu trong file app/Http/Kernel.php

HTTP kernel được mở rộng từ class Illuminate\Foundation\Http\Kernel. Lớp này định nghĩa danh sách bootstrappers sẽ được chạy trước khi request xử lý. Các bootstrappers đó có nhiệm vụ cấu hình xử lý lỗi, cấu hình logging, xác định môi trường của application và thực hiện các tác vụ khác cần được thực hiện (trước khi request được xử lý).

 

HTTP kernel cũng có chức năng định nghĩa 1 danh sách các HTTP middleware mà tất cả các request khác phải chạy qua (trước khi được xử lý từ application). Các middleware này sẽ xử lý một số phần việc như:

Đọc ghi HTTP session
Kiểm tra (nếu application đang trong chế độ maintenance)
Kiểm tra CSRF token
 

Cấu trúc của phương thức handle trong HTTP kernel đơn giản. Nó nhận vào 1 request và trả về 1 response. Kernel lúc đó trở thành 1 hộp đen chứa toàn bộ code xử lý của application. Vì vậy, cung cấp cho nó 1 HTTP request và nó sẽ trả về 1 HTTP response.

 

Service providers
Truy cập các service providers cho application là một trong các hành động khởi động kernel quan trọng nhất. Vì tất cả các service providers cho application được cấu hình ở mảng providers trong file config/app.php. Phương thức register của tất cả provides đó sẽ được gọi. Khi tất cả các providers đã được đăng ký, phương thức boot sẽ được gọi. Service providers cũng chịu trách nhiệm tạo các thành phần khác nhau của framework. Ví dụ như database, queue, validation và route. Vì các thành phần này sẽ cấu hình tất cả tính năng được cung cấp bởi framework. Service provider là thành phần trọng tâm trong quá trình khởi tạo vòng đời request trong Laravel.

 

Router
Sau khi hoàn tất truy cập service loader, request sẽ được chuyển tới router. Router được ví như 1 chốt chặn kiểm tra. Chốt chặn này cũng dễ hiểu giống như bạn đi tìm phụ huynh cho một đứa bé đang bị lạc. Router có nhiệm vụ triểm tra tất cả các route đã được khai báo trong các file ở thư mục routes (so với request được gửi đến). Nếu trùng khớp với 1 route nào đó, thì lúc đó bạn có 2 hướng giải quyết:

Route -> middleware -> controller hoặc action

Route -> controller hoặc action

 

Vì sao có hướng giải quyết này? Vì khi khai báo route, Laravel cho phép bạn ràng buộc request đi qua bằng các middle tự tạo. Do đó, tùy vào mỗi route có ràng buộc middle hay không, chia thành 2 hướng xử lý như trên để bạn linh hoạt tùy theo tình hình thực tế.

 

Middleware
Để ứng dụng có thể xử lý được request mà route đã đăng ký middleware, thì chỉ còn cách vượt qua nó. Middleware sẽ xử lý logic theo những ràng buộc mà bạn (hoặc một người lập trình khác) đã đặt ra. Mục đích là để quyết định request có nên tiếp tục hay không. Ví dụ có 1 request với đường dẫn là http://localhost:8000/login, bạn muốn ràng buộc rằng tồn tại session/cookie đăng nhập của người dùng từ khi vào request này sẽ chuyển về trang chủ. Nếu không, thì nó sẽ vẫn hiển thị biểu mẫu đăng nhập để người dùng tiếp tục sử dụng. Đây là lúc sử dụng middleware để ràng buộc.

 

Các bước xử lý request trong Laravel
Sau khi đã hiểu sơ lược về vòng đời request trong Laravel, chúng ta sẽ tìm hiểu tiếp các bước để xử lý request như thế nào.

 

Bước 1: tiếp nhận request và khởi động bootstrap

Người sử dụng gửi request đến file public/index.php. Đây là file chạy đầu tiên khi có request từ người dùng. Đăng ký cơ chế autoload:

require __DIR__.'/../vendor/autoload.php';
 

Autoload thay thế các lệnh require và include. Trong các file có tên class trùng với tên file và có namespace theo nguyên tắc, hệ thống sẽ tự động vận hành file đó mà không phải sử dụng các lệnh require hoặc include. Import ứng dụng:

$app = require_once __DIR__.'/../bootstrap/app.php';
 

Trong file bootstrap/app.php, nó sẽ thực hiện các nhiệm vụ chính: 

Tạo ứng dụng
Đăng ký các interface cần thiết 
Trả đúng về đối tượng ứng dụng ($app)
 
Bước 2: bắt đầu chạy ứng dụng
Chạy ứng dụng trong file public/index.php. Hệ thống sẽ tự động nhận đối tượng trả về từ bước 1. Sau đó, nó sẽ thực hiện việc xử lý request và trả về response.

$kernel = $app->make(Illuminate\Contracts\Http\Kernel::class);
$response = $kernel->handle(
    $request = Illuminate\Http\Request::capture()
);
$response->send();
$kernel->terminate($request, $response);
 
Bước 3: thực hiện HTTP kernel
HTTP kernel có nhiệm vụ thực hiện các công việc kiểm tra trước khi request được thực thi. Quá trình này được gọi là tiền xử lý các vấn đề như:

Xử lý các lỗi
Cấu hình log
Xác định đúng môi trường
Xác thực phần bảo mật
Khởi động bộ lọc trung gian mặc định và HTTP session
 
Bước 4: khởi tạo các service providers
Service providers là nơi khởi tạo các ứng dụng chạy trong Laravel. Chúng khởi động nhiều thành phần khác nhau trong core và các package được cài bổ sung. 

 
Bước 5: request được chuyển đến router
Khi các service providers đã bắt đầu thực hiện nhiệm vụ của chúng, request sẽ được gửi đến router để định tuyến tới controllers phù hợp. Ở thời điểm đó, bạn sẽ thấy có 2 trường hợp xảy ra: 

Router -> controller -> action

Router -> middleware -> controller -> action 

Ở trường hợp thứ 2, request sẽ phải đi qua bộ lọc trung gian (middleware) trước khi nó vào controller tương ứng. 

 
Bước 6: kích hoạt bộ lọc middleware
Middleware là bộ lọc trung gian để request sau đó tiếp tục đi tiếp hoặc dừng lại. Trong middleware, bạn sẽ thiết lập các điều kiện phù hợp từ phía router để request tiếp tục đi hoặc dừng. Middle thông thường có 3 loại: global middleware, route middleware và middleware groups.

 
Bước 7: xử lý request bằng controller hoặc action
Controller hoặc action sẽ xử lý các request và trả về cho response. Trong quá trình xử lý bước thứ 7 này, bạn nên gọi models để thao tác với cơ sở dữ liệu nhanh hơn. 

 

Bước 8: trả về cho người dùng 
Sau khi request đã được xử lý xong, response sẽ được trả về cho người dùng thông qua view. Một số trường hợp không thông qua views như JSON, XML và Download. 

 
Phương thức xử lý 
Thông thường, chúng ta có 2 phương thức xử lý request: dùng controller hoặc action. Action còn được gọi là closure object. Hai phương thức này đều hoạt động như nhau nhưng cách thể hiện tính năng lại khác nhau. Controller là thành phần trong mô hình MVC. Nó sẽ tối ưu và hiệu năng hơn action. Bạn có thể dễ dàng quản lý và mở rộng request nếu muốn. Còn với action, nó phù hợp với các phương thức xử lý ngắn gọn. Ví dụ như khi ta trả về 1 view, 1 object hoặc array. Action thích hợp khi test request hoặc thực hành trong quá trình học. Cả 2 phương thức controller và action đều cũng trả về response sau khi đã xử lý xong request theo cơ thế hộp đen của HTTP Kernel. 

 
Phương thức trả về 
Phương thức trả về là response về phía người sử dụng. Có 2 cách để ta trả về response: trả về response thông qua view và trả về response không đi qua view. Về cơ bản, 2 hình thức response này cũng không có gì khác biệt. Vì view đã chứa các template đã được khai báo sẵn. Khi trả về thì nó chỉ kèm theo các tham số tùy biến. Khi bạn trả về response thông qua view là chỉ áp dụng cho chuyện truy cập giao diện. Phương thức thường dùng vẫn là phương thức còn lại.
