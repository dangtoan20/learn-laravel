# I. Mô hình vòng đời request Laravel (Request Lifecycle model Laravel)

![image](https://github.com/dangtoan20/learn-laravel/assets/115624538/2083b445-118b-462a-a72f-a3eb3e88dfe3)

# II. Khởi động (Bootstrap)
## Đầu tiên từ phía client sẽ gửi một request (mũi tên màu xanh) đến file public/index.php, nó là đích đến của tất cả các request từ client. Dù code không nhiều nhưng nó là khởi nguyên cho framework.

Mở file public/index.php lên và xem code của nó, các bạn sẽ thấy nó làm 3 nhiệm vụ chính để bootstrap framework.
