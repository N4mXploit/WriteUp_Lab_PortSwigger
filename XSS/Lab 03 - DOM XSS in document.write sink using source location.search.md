<img width="1005" height="621" alt="image" src="https://github.com/user-attachments/assets/dc09c8de-4d8d-4715-b606-9dce600de20e" />

# Tổng quan

Lab có chứa lỗ hổng XSS ở chức năng comment

Sử dụng function `document.write` để search 
Đầu tiên ta thử tìm kiếm như bình thường,sau khi tìm kiếm ta `Ctrl + U` để check phần thẻ script xử lý tìm kiếm thì ta thấy:
```JavaScript
<script>
                        function trackSearch(query) {
                            document.write('<img src="/resources/images/tracker.gif?searchTerms='+query+'">');
                        }
                        var query = (new URLSearchParams(window.location.search)).get('search');
                        if(query) {
                            trackSearch(query);
                        }
</script>            
```
Phân tích code:
 
 -Biến query lấy giá trị từ tham số search từ url.Ví dụ `?search=Hello,World ` => query có giá trị là Hello,World
 
 -Sau đó truyền query vào hàm trackSearch
 
 -Hàm trackSearch() sử dụng document.write() để chèn một ảnh tracking vào trang nhằm ghi nhận từ khóa người dùng tìm kiếm.

Ta có thể thấy phần:

```JavaScript
 document.write('<img src="/resources/images/tracker.gif?searchTerms='+query+'">');
```
document.write có sử dụng để chèn thêm thẻ `<img src="/resources/images/tracker.gif?searchTerms='+query+'">` và phần `+query+` được ghép trực tiếp vào HTML,không được encode hay sanitize trước khi render => XSS

# Khai thác

Ta sử dụng payload:

```JavaScript
"><svg onload=alert(1)>
```

Ở phần Search ta tinh ý thêm "> để đóng thẻ img

Thêm thẻ svg(thẻ hình ảnh vector) 

Ở thẻ svg thêm attribute là onload=alert(1) với ý đồ ở đây là sẽ gọi hàm alert(1) khi load thẻ thành công

<img width="1668" height="889" alt="image" src="https://github.com/user-attachments/assets/d1877cc6-f1f3-4d73-bbf5-3fe3de88ca15" />


Click search và xem kết quả:

<img width="1706" height="970" alt="image" src="https://github.com/user-attachments/assets/9eefb040-4ef9-494e-9fda-0a581ec04626" />

Vậy là ta đã thành công gọi hàm alert

<img width="1677" height="896" alt="image" src="https://github.com/user-attachments/assets/58db45de-d9ab-48ef-8862-4b489432dcf0" />

=> solved
