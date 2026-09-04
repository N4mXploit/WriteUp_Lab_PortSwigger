
<img width="1002" height="515" alt="image" src="https://github.com/user-attachments/assets/3eea67e7-0b79-40de-8140-a5bb6905e4c5" />

---</br>
# Tổng Quan

Khi ta lướt web như một user bình thường khi ta mở ảnh trong tab mới thì url:
```LINK
https://0a21001703a819ce80498f5800de006e.web-security-academy.net/image?filename=20.jpg
```
parameters `filename=....` ta thấy có vẻ là path traversal</br>
Mục tiêu của ta là đọc được `/etc/passwd`
# Khai thác

Ta sử dụng Burp Suite để test thử: 

<img width="1706" height="1018" alt="image" src="https://github.com/user-attachments/assets/f077d086-8ad8-4697-b603-f6ee1c7b0649" /></br>

Ta gửi request vào Burp Repeater</br>
Sang bên Burp Repeater, với parameters `filename=..` ta thử cho payload `../../../../etc/passwd` :

<img width="1702" height="1013" alt="image" src="https://github.com/user-attachments/assets/9d2d98c2-57f4-43e6-8c89-604fde9b151e" />

Sau đó ta bấm send và đợi kết quả:

<img width="1706" height="1017" alt="image" src="https://github.com/user-attachments/assets/f034e0c4-cf55-4675-b33c-584301d328d9" />

Vậy là ta thành công đọc được `/etc/passwd` vậy là hoàn thành Lab

<img width="1706" height="1020" alt="image" src="https://github.com/user-attachments/assets/77ed7cbc-d035-44e9-a0b5-bb3a37e16117" />

=> Solved!

# Kết luận

Nguyên nhân của lỗ hổng là ứng dụng sử dụng trực tiếp giá trị do người dùng cung cấp trong parameter filename để xác định file cần đọc mà không kiểm soát đầy đủ phạm vi của đường dẫn.

Luồng khai thác có thể hình dung:

filename=20.jpg
      ↓
Server lấy giá trị filename
      ↓
Ghép vào đường dẫn file
      ↓
Đọc file từ filesystem

Khi thay đổi thành:

filename=../../../etc/passwd

ta khiến server truy cập ra ngoài thư mục chứa ảnh:

Image directory
     ↓ ../
Parent directory
     ↓ ../
Parent directory
     ↓ ../
/etc/passwd

=> Do đó, đây là một lỗ hổng Path Traversal (Directory Traversal).
