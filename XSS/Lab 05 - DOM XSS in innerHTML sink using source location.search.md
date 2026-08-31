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

Ta có thể thấy là chữ `World!` được `innerHTML` parse nên xuống dòng 

=> rất có thể đây là DOM XSS

Mục tiêu của lab là ta cần gọi ra hàm alert() để solve lab

# Khai thác

Ta thử với payload `<script>alert(1)</script>`:

<img width="1706" height="972" alt="image" src="https://github.com/user-attachments/assets/0642bf05-199d-4ba1-9222-7d77bbfc5f60" />

Có vẻ như phía backend đã chặn payload này

Nếu ta thử payload `<img src=1 onerror="alert(1)">` thì sao:

<img width="1706" height="977" alt="image" src="https://github.com/user-attachments/assets/cacb7a57-859b-4772-9ac9-062a61d2e698" />

Vậy là ta thành công gọi hàm alert(1)

Giải thích : + Trong thẻ img ta có attribute src=1 với mục đích browser sẽ request tới : `https://example.com/1` mà vì nó không tồn tại => thẻ img bị lỗi
             
             + Mà thẻ img bị lỗi là mục đích để ta gọi function alert(1) với attribute là onerror

<img width="1699" height="896" alt="image" src="https://github.com/user-attachments/assets/94d27c73-ccb0-43a4-b4f8-5ab3ecdaabde" />

=> Solved!
