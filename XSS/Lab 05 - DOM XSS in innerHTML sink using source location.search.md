<img width="1003" height="623" alt="image" src="https://github.com/user-attachments/assets/382e71b5-7452-4b3d-b1a0-e7d28c21d1fa" />

# Tổng Quan 
Ta sử dụng trang web như một user bình thường sử dụng các chức năng comment và search.Khi sử dụng search function thì web có để lộ một đoạn code JavaScript:
```JavaScript
<script>
    function doSearchQuery(query) {
        document.getElementById('searchMessage').innerHTML = query;
    }

    var query = (new URLSearchParams(window.location.search)).get('search');

    if (query) {
        doSearchQuery(query);
    }
</script>
```

**Phân tích Code**:

 -Đầu tiên ta có query được lấy từ url, cụ thể là lấy từ paremeter **search**: `var query = (new URLSearchParams(window.location.search)).get('search');` ví dụ: `https://example.com/?search=something` thì query sẽ có giá trị là something
 
 -Sau đó được đưa vào hàm if và dùng `doSearchQuery()` function
 
 -Hàm `doSearchQuery()` function có : `document.getElementById('searchMessage').innerHTML = query;` 
 
 -`document.getElementById('searchMessage')` sẽ tìm thẻ HTML có id là searchMessage và `innerHTML` sẽ dùng để thay đổi nội dung phần tử trong các file html và đương nhiên nó có thể sẽ parse ra thẻ HTML 
 
 => Ta sẽ thử chèn một thẻ bất kỳ

 Với thẻ `Hello <br> World!` :
 
 <img width="1670" height="891" alt="image" src="https://github.com/user-attachments/assets/679c42f9-e538-4dce-94ec-f4fc94e86a50" />

Kết quả cho thấy <br> đã được browser parse thành HTML element thay vì được hiển thị như chuỗi text. Điều này xác nhận input của search đang được đưa vào HTML parsing context.

=> rất có thể đây là DOM XSS

Mục tiêu của lab là ta cần gọi ra hàm alert() để solve lab

# Khai thác

Ta thử với payload `<script>alert(1)</script>`:

<img width="1706" height="972" alt="image" src="https://github.com/user-attachments/assets/0642bf05-199d-4ba1-9222-7d77bbfc5f60" />

Sau khi tìm hiểu trên các tài liệu về DOM Js,Thẻ <script>alert(1)</script> không thực thi khi chèn qua innerHTML vì khi <script> được tạo thông qua việc gán innerHTML, script element này không được thực thi. Đây là hành vi được quy định bởi cơ chế xử lý của HTML DOM và HTML parser.

<img width="1706" height="977" alt="image" src="https://github.com/user-attachments/assets/27896f6d-d216-477c-aae1-23dff3b0fcfe" />

Nếu ta thử payload `<img src=1 onerror="alert(1)">` thì sao:

<img width="1706" height="977" alt="image" src="https://github.com/user-attachments/assets/cacb7a57-859b-4772-9ac9-062a61d2e698" />

Vậy là ta thành công gọi hàm alert(1)

Giải thích : + Trong thẻ img ta có attribute src=1 với mục đích browser sẽ request tới : `https://example.com/1` mà vì src=1 được browser xử lý như một URL tương đối. Khi resource đó không tải thành công, <img> phát sinh error event, từ đó event handler onerror được thực thi.</br>
             + Mà thẻ img bị lỗi là mục đích để ta gọi function alert(1) với attribute là onerror

<img width="1699" height="896" alt="image" src="https://github.com/user-attachments/assets/94d27c73-ccb0-43a4-b4f8-5ab3ecdaabde" />

=> Solved!
