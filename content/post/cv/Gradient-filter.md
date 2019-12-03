---
title: "Filter"
date: 2019-10-22T22:35:22+07:00
draft: false
authors: [Giang]
categories:
  - computer vision
tags:
  - Gradient
  - Filter
  - Edge detection
slug: xac-dinh-cac-canh-trong-anh-bang-gradient
---
# <span style = "color:cyan">Xác định các cạnh trong ảnh bằng gradient filter</span>
## Các mục bài viết
<ul>
<li>1. Cạnh trong ảnh là gì? Mục đích của việc tìm cạnh?</li>
<li>2. Ý nghĩa của gradient và giải thích toán học</li>
<ul>
<li>2.1. Đạo hàm, giải thích toán học và ứng dụng của nó trong giải thích ý nghĩa gradient.</li>
<li>2.2. Ý nghĩa của gradient và gradient filter.</li>
<li>2.3. Mang khái niệm gradient vào ảnh.</li>
<li>2.4. Và sự ra đời của gradient filter</li>

</ul>
</li>
<li>3. Code mô phỏng</li>
</ul>

Với các bài viết ở các chương trước, hẳn các bạn đã nắm được các khái niệm cơ bản về ảnh kĩ thuật số, các loại filter, phương pháp correlation cũng như convolution. Trong bài viết này, mình sẽ mặc định là các bạn đã nắm các khái niệm đó, và toàn bộ bài viết khi đề cập đến ảnh mà không nói gì thêm thì các bạn nên hiểu đó là ***ảnh xám***, với 1 channel màu duy nhất sẽ dễ dàng cho việc lấy ví dụ và minh họa và áp dụng các phép tính. Bài viết này sẽ cho thấy một trong những phương pháp rất phổ biến để trích chọn đặc trưng đặc biệt của ảnh. ***Cạnh***!!!

<!-- https://drive.google.com/open?id=1ze0selFl7aJyfKpFpzsoRFpFnhDQ7iLX -->
 <center><img src="https://drive.google.com/uc?export=view&id=1ze0selFl7aJyfKpFpzsoRFpFnhDQ7iLX" alt="white girl on black background" width="500"/><figcaption>Hình 1. Hình ảnh tham khảo trên mạng</figcaption></center>

Nhìn vào hình bên trên, nếu bạn là một người với đôi mắt tinh tường thì khá chắc là bạn sẽ nhận ra góc cạnh khuôn mặt của cô gái trong bức hình như này.

<!-- https://drive.google.com/open?id=1hWRVYoBxpIkd_mXFtsr2928SCPch1J2B -->
 <center><img src="https://drive.google.com/uc?export=view&id=1hWRVYoBxpIkd_mXFtsr2928SCPch1J2B" alt="filter image" width="500"/><figcaption>Hình 2. Kết quả lấy cạnh của hình trên, các điểm màu xanh biển là các điểm thuộc cạnh</figcaption></center>

## 1. Cạnh trong ảnh là gì? Mục đích của việc tìm cạnh?
Dựa vào hình trên, hãy định nghĩa đơn giản, cạnh là đường nằm giữa 2 vùng ảnh có độ biến thiên mạnh về yếu tố vật lý, mà cụ thể ở đây là màu sắc.

Việc trích xuất các cạnh là một phần của trích xuất đặc trưng trong ảnh, có thể dùng để tách vật thể, phân vùng ảnh trong một vài trường hợp, hoặc dùng để làm dữ liệu cho học máy, học sâu.

Vậy làm thế nào để tìm ra các vùng khác biệt?

## 2. Ý nghĩa của gradient và gradient filter.

### 2.1. Đạo hàm, giải thích toán học và ứng dụng của nó trong giải thích ý nghĩa gradient. 
Phải, toán học, bạn không đọc nhầm đâu, nếu nghĩ đây là một blog giải thích toàn code để ứng dụng thì bạn nhầm rồi. "Ở đây chúng tôi không làm thế", chúng ta ở đây để hiểu rõ các khái niệm và định lý của các phương pháp trích xuất đặc trưng cổ điển.

Gradient trong tiếng anh nghĩa là ***"dốc"*** , ở đây mang ý nghĩa là tìm những pixel mang giá trị lớn hơn hoặc nhỏ hơn hẳn các pixel lân cận, hay nói cách khác độ biến thiên chúng đủ lớn tạo ra những vùng dốc lồi hoặc lõm trong đồ thị.

***"Đạo hàm"*** , một khái niệm gần gũi với mọi người,là phương pháp được truyền đạt rất thông dụng ở các bậc học, từ trung học lên đại học hay thậm chí là cao học, "đạo hàm" với ý nghĩa đơn giản là thể hiện độ chênh lệnh giữa hai giá trị nhưng là tiền đề của những phức tạp đại hơn nhiều. 

Trên một mặt phẳng tọa độ 2D, với hai trục vuông góc, trục hoành là $x$ và trục tung là $y=f(x)$, với $f$ là một phương trình thể hiện độ tương quan của $x$ với $y$. Gọi $x_0$ là một giá trị cách $x$ một khoảng $\Delta x$ ,nghĩa là $\Delta x=x-x_0$. Công thức tính đạo hàm của $f(x_0)$ là: 

<center><font size = "6">$f'(x_0)=\lim\_{\Delta x\to0} \frac{f(x)-f(x_0)}{x-x_0}$</font></center> 



<!-- Nói lại một chút về đạo hàm. Cụ thể mình được dạy ở trường là với mỗi giá trị $y$ là kết quả của phương trình $f(x)$ với biến $x$ biến thiên trong 1 khoảng nhất định. Ứng với mỗi phương trình $f(x)$ luôn tồn tại một phương trình $f'(x)$ thể hiện sự biến thiên hay sự thay đổi của giá trị $y$ tại vị trí $x$ so với giá trị $x$ cách đó 1 khoảng $\Delta x$, tức là vị trí $x - \Delta x$. -->

*Ví dụ nho nhỏ:* đạo hàm của $y=f(x)=a$ với $a$ là một số bất kì, lấy $a=2$, ta có đồ thị:
<!-- https://drive.google.com/open?id=1s9pMlASn0el_fjYfdIPG_PrmLgPnfCK9 -->
 <center><img src="https://drive.google.com/uc?export=view&id=1s9pMlASn0el_fjYfdIPG_PrmLgPnfCK9" alt="y = 2" width="400"/><figcaption>Hình 3. Đồ thị phương trình y = 2</figcaption></center>

 Dễ dàng thấy là giá trị của $y$ không hề thay đổi trên miền $x$ nên sự khác biệt giữa các giá trị $y$ là $0$. Và nếu bạn chưa biết công thức đạo hàm một số bằng $0$ thì có thể ngưng ở đây được rồi.

 Lan man đủ rồi, quay trở lại vấn đề của chúng ta. Với ảnh dưới đây: 
 <!-- https://drive.google.com/open?id=1g17Dz4ahZs5QRNZG1vCfeDxJZjyUxOCY -->
 <!-- https://drive.google.com/open?id=1JHvFCuyRAjIvsCT0KSbE29EQYXF1-eE_ -->
 <center>
<div white-space= nowrap  overflow-y = hidden width = auto>
 <img src="https://drive.google.com/uc?export=view&id=1g17Dz4ahZs5QRNZG1vCfeDxJZjyUxOCY" alt="drawing" width="250"/><img src="https://drive.google.com/uc?export=view&id=1JHvFCuyRAjIvsCT0KSbE29EQYXF1-eE_" alt="light circle" width="250"/>
 <figcaption>Hình 4. Ánh đèn trực tiếp từ nguồn</figcaption>
 </div>
 </center>

Nhìn vào hình gốc bên trái, ta có thể thấy càng gần tâm càng sáng nghĩa là càng gần giá trị $255$ và càng ra rìa càng tối (càng gần $0$). Kích thước ảnh là $160$x$175$, mình lấy toàn bộ giá trị ở hàng giữa (hàng $80$, đường màu đỏ ở hình bên phải) ra để hiển thị trên đồ thị $2D$:
<!-- https://drive.google.com/open?id=1H-4bXlKUzjdXGeDYuV3FEr2HKdaAKucw -->
 <center><img src="https://drive.google.com/uc?export=view&id=1H-4bXlKUzjdXGeDYuV3FEr2HKdaAKucw" alt="value" width="350"/><figcaption>Hình 5. Giá trị độ sáng của các điểm thuộc đường ngang đỏ ở Hình 3</figcaption></center>

Và đồ thị đạo hàm của chính hình trên:
<!-- https://drive.google.com/open?id=1Qw_7_RzD6GBrFGgLF7D9mhDhUog1twRG -->
<center><img src="https://drive.google.com/uc?export=view&id=1Qw_7_RzD6GBrFGgLF7D9mhDhUog1twRG" alt="gradient" width="350"/><figcaption>Hình 6. Đạo hàm của đồ thị trên</figcaption></center>

Với đoạn màu sắc biến đổi ***mạnh*** đồ thị hình thành các ***dốc*** rất rõ rệt, ở vùng màu sắc bão hòa (vùng trung tâm toàn trắng), không còn biến thiên nên đồ thị trải dài ở giá trị $0$. Điều này thể hiện các pixel nằm ở tọa độ có độ biến thiên mạnh chính là các pixel thuộc cạnh của vật thể. Nhưng thế nào thì gọi là "***mạnh***"? Không có giới hạn nào cho việc đó cả, buộc chúng ta phải đặt ra một ngưỡng giá trị để quyết định pixel đó là ***cạnh*** hay không, nói một cách toán học $f'(x) >threshold$ thì chấp nhận là điểm thuộc cạnh. Phần này sẽ nói rõ hơn ở dưới.

### 2.2. Mang khái niệm gradient vào ảnh.

Ở đồ thị 2D, ở mỗi tọa độ chúng ta có 1 cặp $(x,y)$ tức là tương ứng với mỗi giá trị $x$ có 1 giá trị $y$. Trong một bức ảnh tuy là 2D với từng cặp $(x,y)$ nhưng ứng vỗi mỗi cặp đó là giá trị độ sáng của pixel, gọi giá trị này là "$l$", phương trình của chúng ta giờ đây là $l=f(x,y)$. Vậy ứng với mỗi pixel của ảnh, ta sẽ có giá trị đạo hàm theo 2 hướng $x$ và $y$, có thể viết dưới dạng vector là <span style="font-size:larger;">$l'=f'(x,y)=[\frac{df}{dx};\frac{df}{dy}]$</span>.

<!-- https://drive.google.com/open?id=1V-EwwUJjFgYmqu97Zzf_Y2LtMNUcJXPy -->
<center><img src="https://drive.google.com/uc?export=view&id=1V-EwwUJjFgYmqu97Zzf_Y2LtMNUcJXPy" alt="Black and white image" width="200"/><figcaption>Hình 7. Ảnh xám biến thiên theo trục hoành.</figcaption></center>

Hình này mang kích thước $256$x$256$, lấy các giá trị tại hàng $y=128$ và $x=128$ của hình này ta sẽ có:

<!-- https://drive.google.com/open?id=1F_PacMGnu1-jWnqLc0O_evCQxiJg8nDl -->
<!-- https://drive.google.com/open?id=1juYOOJ5Cbrg8lnNrwj4MR9rAxb3bgn1B -->
<center>
<div white-space= nowrap  overflow-y = hidden width = auto>
<center><img src="https://drive.google.com/uc?export=view&id=1F_PacMGnu1-jWnqLc0O_evCQxiJg8nDl" alt="row" width="300"/><img src="https://drive.google.com/uc?export=view&id=1juYOOJ5Cbrg8lnNrwj4MR9rAxb3bgn1B" alt="column" width="300"/>
<figcaption>Hình 8. Giá trị của Hình 7 của hàng giữa và cột giữa</figcaption>
</div>
</center>

Như đã thấy, hình trên giá trị tăng dần đều theo $y$, nghĩa là giá trị đạo hàm bằng một hàm số nhất định. Hình dưới là giá trị theo cột, cụ thể là cột số $128$, tất cả giá trị đều là hằng số $127$, tức đạo hàm của mọi giá trị trong cột này đều bằng $0$. Vậy đạo hàm của mọi điểm trên hình này đều có thể viết lại thành công thức tổng quát là <span style="font-size:larger;">$f'(x,y)=[\frac{df}{dx};0]$</span>.

<!-- https://drive.google.com/open?id=1IhGbOHn3w9oVnZHB8UzZyNxdea8kGBML -->
<center><img src="https://drive.google.com/uc?export=view&id=1IhGbOHn3w9oVnZHB8UzZyNxdea8kGBML" alt="Gray" width="200"/><figcaption>Hình 9. Ảnh xám biến thiên theo trục tung</figcaption></center>

Nếu quay hình này một góc $90^\text{o}$ như trên thì công thức mới sẽ là <span style="font-size:larger;">$f'(x,y)=[0;\frac{df}{dy}]$</span>.

 Nhưng đời lúc nào cũng thế thì lại thành dễ quá, cuộc sống khắc nghiệt hơn nhiều, thực tế rất hiếm trường hợp chúng ta gặp được sự thay đổi màu sắc theo một chiều như vậy. Đa số chúng ta sẽ đối mặt với trường hợp biến thiên theo cả hai chiều, như hình này:

 <!-- https://drive.google.com/open?id=1CYP8DLl93A0EhTBooizv1FurnauWZb15 -->
 <!-- https://drive.google.com/open?id=1_Na8ZpkMK--e2unsgbMqsc5sMhs1edJ8 -->
 <center>
<div white-space= nowrap  overflow-y = hidden width = auto>
 <img src="https://drive.google.com/uc?export=view&id=1CYP8DLl93A0EhTBooizv1FurnauWZb15" alt="hor+vec" width="200"/><img src="https://drive.google.com/uc?export=view&id=1_Na8ZpkMK--e2unsgbMqsc5sMhs1edJ8" alt="hor+vec_marked" width="200"/>
 <figcaption>Hình 10. Ảnh xám biến thiên chéo</figcaption>
 </div>
 </center>

<!-- Nhờ mẹ thiên nhiên, thuyết tiến hóa, và sự cố gắng của ông cha ta trong hơn hai triệu năm (hoặc có thể tìm kênh youtube ***Đen Vâu Official*** để biết rõ hơn) -->
 Trải qua hơn hai triệu năm tiến hóa và phát triển, giờ đây với mắt thường chúng ta có thể nhận ra ở hình gốc bên trái trên các đường được đánh dấu như hình bên phải, độ sáng của cả đường ngang và được dọc giảm dần từ trái qua phải và từ trên xuống dưới, biểu diễn lên đồ thị như sau:

<!-- https://drive.google.com/open?id=1Kq3NYcmVtBJWZ9GwmdDS3i9ftdBBI8Rq -->
<center><img src="https://drive.google.com/uc?export=view&id=1Kq3NYcmVtBJWZ9GwmdDS3i9ftdBBI8Rq" alt="Value" width="300"/><figcaption>Hình 11. Độ sáng giảm theo cả hai chiều của Hình 10</figcaption></center>

Ở đây ta có thể thấy sự biến thiên trên cả $x$ và $y$ nên giá trị của đạo hàm tại điểm $x,y$ sẽ phụ thuộc vào cả 2 biến theo công thức gốc <span style="font-size:larger;">$l'=f'(x,y)=[\frac{df}{dx};\frac{df}{dy}]$</span>. Hãy cùng nhìn công thức này một cách đơn giản nhất, ta biết nó là một vector nên có quyền viết nó lại là <span style="font-size:larger;">$\overrightarrow{v}=[v1,v2]$</span>.

<!-- https://drive.google.com/open?id=1KLfvNRAiSfvJ8ea5_xU_p5Tj6bGxOs52 -->
<center><img src="https://drive.google.com/uc?export=view&id=1KLfvNRAiSfvJ8ea5_xU_p5Tj6bGxOs52" alt="A vector" width="300"/><figcaption>Hình 12. Vector V, nhìn thân thiện hơn hẳn đúng không nào</figcaption></center>


Trong đại số tuyến tính, độ lớn vector <span style="font-size:larger;">$\overrightarrow{v}$</span> sẽ được tính bằng công thức <span style="font-size:larger;">$\lVert \overrightarrow{v} \rVert = \sqrt {v1^2+v2^2}$</span> và góc $\theta$ tạo bởi vector và trục hoành của trục tọa độ là <span style="font-size:larger;">$\theta=\arctan(\frac{v2}{v1})$</span>. Việc giải thích các công thức này sẽ nằm ở một bài khác. Giờ chúng ta chuyển lại về công thức của chúng ta, ta có:

<center><font size = "5"> $\lVert f'(x,y) \rVert = \sqrt {(\frac{df}{dx})^2+(\frac{df}{dy})^2}$</font></center>

<center><font size = "5"> $\theta=\arctan(\frac{df}{dy}/\frac{df}{dx})$ </font></center>

### 2.3. Và sự ra đời của gradient filter

Công thức là thế, định nghĩa là thế nhưng làm thế nào để đưa công thức đó vào filter mà áp vào ảnh được (sử dụng tích chập hoặc tương quan chéo, chả quan trọng mấy đâu vì các filter này đều được thiết kế cho đối xứng qua tâm cả). Đầu tiên, phải làm rõ một điều, các giá trị trên ảnh là các biến rời rạc, nếu ảnh của bạn là $32$x$32$, nghĩa là có $1024$ pixels và có $1024$ giá trị độ sáng, không hơn, không kém.

Cùng nhìn nhận lại công thức gốc của đạo hàm:

<center><font size = "5"> $f'(x_0)=\lim\_{\Delta x\to0} \frac{f(x)-f(x_0)}{x-x_0}$</font></center> 

Công thức này áp dụng khi $x$ là một biến liên tục thì $x_0$ là một đoạn rất nhỏ cách sau $x$, phải đủ nhỏ để khoảng $f(x)$ biến thiên là một khoảng tuyến tính. Tuy nhiên giá trị độ sáng của ảnh là những giá trị rời rạc, nằm cách nhau từng pixel và có giới hạn nên công thức có thể viết lại như sau:

<center><font size = "5">$f'(x)=\frac{f(x+1)-f(x)}{1}$</font></center> 



Áp lên ảnh 2 chiều $x$ và $y$, và nhân với 1, ta có:

<center><font size = "5">$\frac{df(x,y)}{dx}=f(x+1,y)-f(x,y)$</font></center> 
<center><font size = "5">$\frac{df(x,y)}{dy}=f(x,y+1)-f(x,y)$</font></center> 

Đến đây chắc hẳn các bạn đã có những tưởng tượng cơ bản cho filter của chúng ta rồi, filter khi áp vào ảnh sẽ phải thể hiện giá trị hiệu của 1 pixel và pixel kế cận. 

<!-- https://drive.google.com/open?id=1g9LWHWNuVSUuAkeOwyd4igLKzm3_wuaZ -->
<center><img src="https://drive.google.com/uc?export=view&id=1g9LWHWNuVSUuAkeOwyd4igLKzm3_wuaZ" alt="Fool Kernel" width="200"/><figcaption>Hình 13. Filter chính xác như công thức nhé</figcaption></center>

Và chúng ta có ngay một filter thỏa mãn công thức. Kết thúc blog hôm nay, tất cả đều vui vẻ!...Đùa đấy, nhìn vào đây sẽ thấy ngay filter này có vấn đề đúng không, nó không hề có pixel trung tâm ở filter này. Vậy hãy biến đổi ma trận này một chút. 

<!-- https://drive.google.com/open?id=1MrrJvqJS4H8fVkFi2ofEY5voeZh0YuNV -->
<!-- https://drive.google.com/open?id=1la1vy5P2LVKGXw6t4kwhem3ltobdA-w9 -->
<!-- <center><img src="https://drive.google.com/uc?export=view&id=1MrrJvqJS4H8fVkFi2ofEY5voeZh0YuNV" alt="Prewitt filter" width="200"/><img src="https://drive.google.com/uc?export=view&id=1la1vy5P2LVKGXw6t4kwhem3ltobdA-w9" alt="y = 2" width="225.8"/><figcaption>Hình 14. Prewitt filter cho trục hoành (trái) và trục tung (phải)</figcaption></center> -->

<center>
<div white-space= nowrap  overflow-y = hidden width = auto>
    <img src="https://drive.google.com/uc?export=view&id=1MrrJvqJS4H8fVkFi2ofEY5voeZh0YuNV" alt="Prewitt filter" width="200"/>
    <img src="https://drive.google.com/uc?export=view&id=1la1vy5P2LVKGXw6t4kwhem3ltobdA-w9" alt="y = 2" width="225.8"/>
</div>
<figcaption>Hình 14. Prewitt filter cho trục hoành (trái) và trục tung (phải)</figcaption>
</center>

Filter này có tên là Prewitt, là một trong những gradient filter cơ bản, giải quyết đúng được vấn đề mà chúng ta đề cập từ đầu bài đến giờ:

* Filter có số cạnh lẻ $\rightarrow$ giá trị khi áp filter với phương pháp tương quan chéo sẽ thu về được giá trị biến thiên vào pixel trung tâm filter.
  
* Thể hiện được sự biến thiên từ trái qua phải và từ trên xuống dưới.

## 3.Code mô phỏng
Mình sẽ làm một đoạn code ngắn áp thử hai filter này vào ảnh với hai thư viện ***opencv-python*** và ***numpy***. Đầu tiên, cực kì quan trọng, gán hai thư viện:

```python
import numpy as np
import cv2
```

Mình sẽ dùng tương quan chéo để áp filter vào hình nên sẽ viết một hàm tương quan chéo với hai ma trận có cùng kích thước:

```python
def cross_correlation(array_1, array_2):
    return np.sum(array_1*array_2)
```
Thế là xong phần chuẩn bị, giờ vào phần chính, mình sẽ thực nghiệp trên bức ảnh này:

<!-- https://drive.google.com/open?id=17sTPaTESRiirBrLzDZX_zDsM-JKeU3YR -->
<center><img src="https://drive.google.com/uc?export=view&id=17sTPaTESRiirBrLzDZX_zDsM-JKeU3YR" alt="objects" width="200"/><figcaption>Hình 15. Tròn, vuông, tam giác</figcaption></center>

Khởi tạo hai filter và đọc ảnh thành dạng ***Blue-Green-Red***:

```python
filter_x = np.array([[-1, 0, 1],
                     [-1, 0, 1],
                     [-1, 0, 1]]).astype(np.int32)
filter_y = np.array([[1 , 1, 1],
                     [0 , 0, 0],
                     [-1,-1,-1]]).astype(np.int32)

image = cv2.imread('''image_path''',0).astype(np.int32)
```

Nói thêm về phần "***np.int32***", một bức ảnh khi đọc vào bằng opencv sẽ ở dạng ***unsigned integer 8-bit***, nghĩ là giá trị bị giới hạn bởi khoảng $2^8 = 256$, tức khoảng $[0,255]$. Khi chúng ta tính toán ra với một giá trị lớn hơn $255$ giá trị đó sẽ đếm ngược về $0$. 

Ví dụ: Với phép tính $250+10$ ta sẽ có giá trị bằng $4$.

***astype(np.int32)*** sẽ chuyển các giá trị trong ma trận này thành giá trị nguyên $32$-bit. Bằng cách này chúng ta có thể tính toán bình thường. Rồi tiếp nối là các bước lặt vặt:

```python
# Lấy kích thưởng của ảnh
shape = image.shape

# Thêm padding = 1 vào ảnh gốc
image_pad = np.zeros((shape[0]+2,shape[1]+2))
image_pad[1:(shape[0]+1),1:shape[1]+1]=image

# Tạo hai bức ảnh giá trị 0 để mang cách số tính toán
result_x = np.zeros_like(image)
result_y = np.zeros_like(image)
```

Chọn giá trị ***stride*** là $1$, ta cho filter trượt dần trên ảnh theo từng hàng và cột:
```python
for i in range(shape[0]):
    for j in range(shape[1]):
        result_x[i,j] = cross_correlation(image_pad[i:i+3,j:j+3],filter_x)
        result_x[i,j] = cross_correlation(image_pad[i:i+3,j:j+3],filter_y)
```

Sau khi tính toán xong, các giá trị lớn hơn $255$ phải được trả về $255$ và tương tự với các giá trị bé hơn $0$ để đảm bảo đúng kiểu dữ liệu của hình (uint8) của hình:

```python
# Tạo ma trận mang các giá trị boolean
mask_1 = result_x < 0
mask_2 = result_y > 255
mask_3 = result_x < 0
mask_4 = result_y > 255

result_x[mask_1] = 0
result_x[mask_2] = 255
result_y[mask_3] = 0
result_y[mask_4] = 255
```
<!-- https://drive.google.com/open?id=1_6J9KwRQdEhYgAb9nU25-GSa5lcQwOqG -->
<!-- https://drive.google.com/open?id=1JWSpvB3EvQDDk1Xg1M7ltBU54WTS18vG -->
<center>
<div white-space= nowrap  overflow-y = hidden width = auto>
<img src="https://drive.google.com/uc?export=view&id=1_6J9KwRQdEhYgAb9nU25-GSa5lcQwOqG" alt="X" width="200"/><img src="https://drive.google.com/uc?export=view&id=1JWSpvB3EvQDDk1Xg1M7ltBU54WTS18vG" alt="Y" width="200"/>
<figcaption>Hình 16. Bên trái là biến thiên theo trục hoành và bên phải ngược lại </figcaption>
</div>
</center>

Với hình bên trái, những điểm màu trắng chính là những điểm có biến thiên mạnh theo trục hoành, ngược lại hình bên trái thể hiện các điểm biến thiên mạnh theo trục tung. Vậy mỗi tọa độ trên ảnh giờ sẽ mang một vector thể hiện độ lớn biến thiên tại điểm đó theo hai hướng $x$ và $y$. Công thức tính độ lớn tại pixel đó là: 

## $\lVert f'(x,y) \rVert = \sqrt {(\frac{df}{dx})^2+(\frac{df}{dy})^2}$

Chọn một ngưỡng threshold, chúng ta quyết định một điểm có là cạnh hay không nếu độ lớn của nó vượt ngưỡng, giá trị của biến threshold này phụ thuộc vào kinh nghiệm của người viết code, ở đây mình sẽ chọn giá trị này là 80. Viết thành code như sau:

```python
result = np.sqrt(result_1**2+result_2**2)

# tạo một mask mang các giá trị bé hơn 80 và ngược lại
mask_0 = result < 80
mask_255 = result >= 80
result[mask_0] = 0
result[mask_255] = 255
```

<!-- https://drive.google.com/open?id=1cXJ5bAkulwI4IMWl3s28-PreNsW_wetm -->
<center><img src="https://drive.google.com/uc?export=view&id=1cXJ5bAkulwI4IMWl3s28-PreNsW_wetm" alt="X" width="200"/><img ><figcaption>Hình 17. Và thế là ta có kết quả các đường cạnh trắng rất đẹp đẽ.</figcaption></center>

## Thảm khảo:
1. [Computer Vision at Udacity](https://classroom.udacity.com/courses/ud810/lessons/2818558556/concepts/35185989740923) 
2. [Opencv package](https://opencv.org/)
3. [Numpy package](https://numpy.org/)
4. [Mathematic courses](https://www.khanacademy.org/)










































