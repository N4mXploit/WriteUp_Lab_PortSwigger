<img width="1005" height="509" alt="image" src="https://github.com/user-attachments/assets/31833868-c2fa-4c60-b61e-c1c706a20043" />

# Tổng quan

Lab chứa lỗ hổng XSS ở chức năng comment 
Mục tiêu của ta là gọi hàm alert ở chức năng comment

# Khai thác

Đầu tiên ta click vào view post bất kỳ,kéo xuống ta sẽ thấy phần Post Comment:

<img width="1255" height="671" alt="image" src="https://github.com/user-attachments/assets/8fdad64f-85ab-416d-af9c-497115772ecc" />

Với phần comment,ta test thử với payload:

```HTML
Hello <br> World
```

Nếu từ World bị xuống dòng tức web có render thẻ html => ta có thể thử với thẻ `<script></script>`

<img width="904" height="660" alt="image" src="https://github.com/user-attachments/assets/86ac9791-ce76-403f-9cfa-a948d6cc7b3c" />

Ta click Post Comment sau đó check phần Comment mình vừa post

<img width="1666" height="893" alt="image" src="https://github.com/user-attachments/assets/39814f6d-5ffd-4326-a495-9b25aa0f2168" />

Sau khi check phần Comment của bài Post ta thấy rằng từ World bị xuống dòng do thẻ `<br>` => ta có thể thử thẻ `<script>`:

```JavaScript
<script>alert(1)</script>
```
<img width="1685" height="683" alt="image" src="https://github.com/user-attachments/assets/1554df52-6e9e-4681-8cc8-201705d37c6f" />

Click vào Post Comment và quay lại để xem kết quả:

<img width="1700" height="967" alt="image" src="https://github.com/user-attachments/assets/d39120a0-3535-44ad-97ab-3045422e6a96" />

Vậy là ta gọi hàm alert thành công 

<img width="1674" height="897" alt="image" src="https://github.com/user-attachments/assets/b4e54b4c-0f46-437e-940d-549a18153128" />

=> solved!
