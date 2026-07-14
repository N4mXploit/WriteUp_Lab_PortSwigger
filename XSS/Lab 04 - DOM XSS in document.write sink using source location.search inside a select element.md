<img width="1002" height="672" alt="image" src="https://github.com/user-attachments/assets/edae93a6-d504-4edf-b439-3b80efdb8dbc" />

# Tổng quan

Lab có chứa lỗ hổng XSS DOM trong chức năng `Check stock`.

Đầu tiên ta truy cập vào lab như một client bình thường khi ta bấm vào `View Details` thì ta thấy có một chức năng `Check stock` ta thử inspect và `Ctrl+F` để check xem button `Check stock` được xử lí như nào?

Ta thấy
```
<form id="stockCheckForm" action="/product/stock" method="POST">
                            <input required="" type="hidden" name="productId" value="1">
                            <script>
                                var stores = ["London","Paris","Milan"];
                                var store = (new URLSearchParams(window.location.search)).get('storeId');
                                document.write('<select name="storeId">');
                                if(store) {
                                    document.write('<option selected>'+store+'</option>');
                                }
                                for(var i=0;i<stores.length;i++) {
                                    if(stores[i] === store) {
                                        continue;
                                    }
                                    document.write('<option>'+stores[i]+'</option>');
                                }
                                document.write('</select>');
                            </script><select name="storeId"><option>London</option><option>Paris</option><option>Milan</option></select>
                            <button type="submit" class="button">Check stock</button>
                        </form>
```

Có thẻ script để xử lí event cho check stock,ta tiến hành phân tích đoạn code JavaScript trong thẻ script:

```Javascipt
var stores = ["London","Paris","Milan"];
var store = (new URLSearchParams(window.location.search)).get('storeId');
document.write('<select name="storeId">');
if(store) {
document.write('<option selected>'+store+'</option>');
}
for(var i=0;i<stores.length;i++) {
if(stores[i] === store) {
continue;
}
document.write('<option>'+stores[i]+'</option>');
}
document.write('</select>');
```

Đoạn code trên lấy giá trị storeId trực tiếp từ URL. Vì dữ liệu này do người dùng kiểm soát, nên store được xem là nguồn dữ liệu (source). Nếu giá trị của store được đưa vào các hàm như document.write thì rất có thể là XSS 
Cuối đoạn code có điền thêm thẻ đóng `</select>`
Luồng thực thi của chương trình

Giả sử URL:

```
https://example.com/?storeId=Paris
|
v
Tạo mảng:
stores = ["London","Paris","Milan"];
|
v
Đọc tham số URL:
store = "Paris";
|
v
Ghi:
<select name="storeId">
|
v
Ghi:
<option selected>Paris</option>
|
v
Duyệt mảng:
London → thêm <option>London</option>
Paris → bỏ qua (continue)
Milan → thêm <option>Milan</option>
|
v
Đóng:
</select>
```
Mục tiêu của lab là gọi ra hàm alert
# Khai thác 

Ta sử dụng payload:
```
&storeId=1"><select><svg%20onload=alert(1)>
```
`&storeId=1">` với mục đích là để đóng cái check stock đó đi
`<select>` là để mở thẻ Select vì phía sau đoạn code có write thêm thẻ đóng Select 
`<svg%20onload=alert(1)>` mục đích là tạo file ảnh vecto thêm attribute onload=alert(1) với mục đích khi thẻ svg được load thì sẽ thực thi lệnh alert(1) đúng với mục tiêu của lab

Ta enter vào url và đợi kết quả:

<img width="1687" height="965" alt="image" src="https://github.com/user-attachments/assets/06e9f35b-b4eb-401a-bbd3-84ba9e833fd1" />
_____________
<img width="1706" height="970" alt="image" src="https://github.com/user-attachments/assets/a370f8f6-2445-4679-8dcb-91e454607d0c" />
_____________
<img width="1679" height="892" alt="image" src="https://github.com/user-attachments/assets/a80e7bcf-2f62-44a7-8f13-64366e755c34" />

=> Solved!
