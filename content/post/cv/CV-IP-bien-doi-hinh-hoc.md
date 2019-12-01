[TOC]

# Biến đổi hình học - Geometric transformation

##  1. Giới thiệu biến đổi hình học

Khi nói về biến đổi hình học, ta thường nghĩ đến gì đầu tiên? Cùng lấy ví dụ để dễ hình dung hơn. Đầu tiên ta có cuốn tập bên trái như Hình 1, làm thế nào để biến đổi đối tượng (quyển sách) có được vị trí, kích thước, góc cạnh giống như trên hình bên phải?

<p>
    <center><img src="https://scontent.fsgn2-1.fna.fbcdn.net/v/t1.0-9/77264899_2288492418109355_3553000683319853056_n.jpg?_nc_cat=105&_nc_ohc=Pa5t_o-3bIMAQn0TMs_pMXWIWbq1J8TxYLQgJUFe7WQ_A_APUJjpvODtQ&_nc_ht=scontent.fsgn2-1.fna&oh=48efca7303baa8e73546da67804a307b&oe=5E444C70"width=500></center>
<center> Hình 1: Ví dụ minh họa phép biến đổi hình học [2]
</p>

Từ đối tượng ở hình bên trái, ta cần **căn chỉnh tỉ lệ**, sau đó **tịnh tiến** đến vị trí phù hợp, cuối cùng là **xoay** một góc sao cho kết quả ta được hình bên phải.

## 2. Biến đổi hình học thực chất là gì?

Vậy biến đổi hình học là gì? Biến đổi hình học là một ánh xạ một-một từ một tập hợp điểm sang một tập hợp điểm khác dựa trên cùng một quy tắc. Nói một cách khái quát dễ hiểu, *phép biến đổi hình học là các phép ánh xạ tọa độ điểm hay vector thành tọa độ hay vector khác* [4]. 

Ví dụ ta có các phép biến đổi như Hình 2:

<p>
    <center><img src="https://scontent.fsgn4-1.fna.fbcdn.net/v/t1.0-9/74528893_2275467919411805_7772381734640812032_n.jpg?_nc_cat=100&_nc_oc=AQm5-gA9-OJBXH0WQuXJzO5n6DY9wC9rQUCAXQsO8XG7lGxoMzkjnzwpblp9rIV2IFs&_nc_ht=scontent.fsgn4-1.fna&oh=d586a46ea482438a0e92e65099ef90b0&oe=5E4F7C98"width=500></center>
<center> Hình 2: Một số phép biến đổi hình học [2]
</p>

## 3. Các phép Biến đổi hình học thường gặp

### a. Phép biến đổi tuyến tính - Linear transformation

* ***Phép tỉ lệ*** - ***Scale***: thay đổi kích thước ảnh theo tỉ lệ của từng trục:
  $$
  x' = ax\\y' = by
  $$
  
  Ta có thể tìm được ma trận kết hợp $a$, $b$. Khi ta nhân ma trận tỉ lệ $\bold{M}$ có kích thước $2$x$2$ cho ma trận tọa độ điểm ảnh có kích thước $2$x$1$ ta được tọa độ mới tỉ lệ đúng bằng:
  $$
  \begin{bmatrix}
  x'\\
  y'
  \end{bmatrix} = \bold{M}
  \begin{bmatrix}
  x\\
  y
  \end{bmatrix}=
  \begin{bmatrix}
  a & 0\\
  0 & b
  \end{bmatrix}
  \begin{bmatrix}
  x\\
  y
  \end{bmatrix}
  $$

<p>
    <center><img src="https://github.com/nguyenphan99/Geometric_Transformation/blob/master/images/rose_scaled.gif?raw=true"width=400></center>
<center> Hình 3: Minh họa phép tỉ lệ. Vùng màu đen là vùng không có điểm ảnh, tương tự cho các hình còn lại.
</p>

* ***Phép xoay*** - ***Rotation***:  Xét 1 điểm $x$ trên ảnh làm đại diện, xoay $x$ một góc $\theta$ - góc tính từ tọa độ $x$ ban đầu đến $x'$ sau khi đã xoay
  $$
  x' = x\cos\theta - y\sin\theta\\
  y' = x\sin\theta + y\cos\theta\\
  $$
  Tương tự ta cũng tìm được ma trận $\bold{M}$:
  $$
  \begin{bmatrix}
  x'\\
  y'
  \end{bmatrix} = \bold{M}
  \begin{bmatrix}
  x\\
  y
  \end{bmatrix}=
  \begin{bmatrix}
  \cos\theta & - \sin\theta\\
  \sin\theta & \cos\theta
  \end{bmatrix}
  \begin{bmatrix}
  x\\
y
  \end{bmatrix}
  $$
  
  <p>
    <center><img src="https://github.com/nguyenphan99/Geometric_Transformation/blob/master/images/rose_rotated.gif?raw=true"width=400></center>
  <center> Hình 4: Minh họa phép xoay
  </p>
  
* ***Phép trượt nghiêng*** - ***Shear***: tương tự phép tỉ lệ thì phép trượt nghiêng có thể hiểu là thay đổi độ dài theo đường chéo: 
  $$
  x' = x + ay\\
  y' = bx+ y\\
  $$
  Tương tự ta cũng tìm được ma trận $\bold{M}$:
  $$
  \begin{bmatrix}
  x'\\
  y'
  \end{bmatrix} = \bold{M}
  \begin{bmatrix}
  x\\
  y
  \end{bmatrix}=
  \begin{bmatrix}
  1 & a\\
  b & 1
  \end{bmatrix}
  \begin{bmatrix}
  x\\
  y
  \end{bmatrix}
  $$

  <p>
      <center><img src="https://github.com/nguyenphan99/Geometric_Transformation/blob/master/images/rose_sheared.gif?raw=true"width=400></center>
  <center> Hình 5: Minh họa phép trượt nghiêng
  </p>


Ngoài ra còn có các phép biến đổi lật ảnh theo trục $Ox$, lật ảnh theo trục $Oy$,...Tổng quát công thức chung:

$\bold{X'}$ là điểm ảnh sau khi biến đổi, có tọa độ ($x', y'$)

$\bold{X}$ là điểm ảnh được biến đổi, có tọa độ ($x, y$)

$p$ là tham số - thường là ma trận $2$x$2$ ký hiệu $\bold{M}$

$f$ là hàm biến đổi.
$$
\bold{X'} = f(\bold{X};p)\  \rightarrow
\begin{bmatrix}
   x' \\
   y'
\end{bmatrix} = \bold{M}
\begin{bmatrix}
   x \\
   y
\end{bmatrix}
$$

Ta tổng hợp một số ma trận biến đổi tuyến tính như sau:
$$
\def\arraystretch{2.0}
  
     \begin{array}
     {c:c:c}
     {Scale\\ \bold{M}=
     \begin{bmatrix}
    	 a & 0\\
   	 0 & b
     \end{bmatrix}}
     &
     {Flip\ Oy\\ \bold{M}=
    \begin{bmatrix}
    	 -1 & 0\\
   	 0 & -1
     \end{bmatrix}}
    \\ \hline
    {Rotation\\ \bold{M}=
     \begin{bmatrix}
    \cos\theta & -\sin\theta \\
  \sin\theta &  \cos\theta \\
     \end{bmatrix}}
     &    
     {Flip\  Ox\\ \bold{M}=
    \begin{bmatrix}
    	 -1 & 0\\
   	 0 & 1
     \end{bmatrix}}
    \\ \hline
     {Shear\\ \bold{M}=
     \begin{bmatrix}
  	1 & a\\
  	b & 1
     \end{bmatrix}} & 
   
   {Identity\\ \bold{M}=
    \begin{bmatrix}
    	 1 & 0\\
   	 0 & 1
     \end{bmatrix}}
     
  \end{array}
$$

Một số tính chất của phép biến đổi tuyến tính:

* Giữ nguyên gốc tọa độ
* Đường thẳng ánh xạ thành đường thẳng
* Bảo toàn tính song song
* Tỉ lệ được giữ nguyên
* Bảo toàn tổ hợp tuyến tính (tổ hợp của các phép biến đổi tuyến tính cũng là tuyến tính)

### b. Phép tịnh tiến - Translation

Đối với phép tịnh tiến, ta có thể áp dụng tương tự như tuyến tính để tìm ma trận biến đổi $\bold{M}$ theo $t_x$ và $t_y$? Với $t_x$ và $t_y$ lần lượt là độ dịch chuyển theo hai trục $O_x$ và $O_y$.
$$
x' = x +t_x\\
y' = y +t_y\\
$$
Đáp án là **không thể** bởi vì không có ma trận $2$x$2$ nào cho tham số phù hợp mà độc lập với $x$, $y$.
$$
\begin{bmatrix}
x'\\
y'
\end{bmatrix} = \bold{M}
\begin{bmatrix}
x\\
y
\end{bmatrix} =
\begin{bmatrix}
? & ?\\
? & ?
\end{bmatrix}
\begin{bmatrix}
x\\
y
\end{bmatrix}
$$
Vì vậy, thay vì biếu diễn trên mặt phẳng 2 chiều ta chiếu sang không gian 3 chiều bằng cách thêm 1 vào tọa độ $(x, y)$ để được hệ tọa độ đồng nhất $(x, y, 1)$, hệ số này sẽ **độc lập** với $x$, $y$. 

Hệ tọa độ đồng nhất được xác định bởi tỉ lệ, ta có thể hình dung rằng khi dịch chuyển một vật lại gần hoặc ra xa tầm mắt sẽ làm cho kích thước của vật thay đổi, tương đương với khoảng cách từ mắt đến vật thay đổi.
$$
\begin{bmatrix}
   x \\
   y
\end{bmatrix} \Rightarrow
\begin{bmatrix}
   x \\
   y \\
   1
\end{bmatrix}
\stackrel{def}{=}	
\begin{bmatrix}
  ax \\
  ay \\
  a
\end{bmatrix}
\\
\\
$$
Với hệ tọa độ đồng nhất, ta tìm được một ma trận $3$x$3$ thỏa mãn phép tịnh tiến như sau:
$$
\begin{bmatrix}
   x' \\
   y' \\
   1
\end{bmatrix} = \bold{M}
\begin{bmatrix}
   x \\
   y \\
   1
\end{bmatrix} = 
\begin{bmatrix}
   1 & 0 & t_{x} \\
   0 & 1 & t_{y} \\
   0 & 0 & 1
\end{bmatrix}
\begin{bmatrix}
   x \\
   y \\
   1
\end{bmatrix}
$$
Và đây là hai ví dụ cho phép tịnh tiến (Hình 6 và Hình 7):

<p>
    <center><img src="https://github.com/nguyenphan99/Geometric_Transformation/blob/master/images/rose_translated.gif?raw=true"width=400></center>
<center> Hình 6: Minh họa phép tịnh tiến trong thực tế
</p>

<p>
    <center><img src="https://scontent.fsgn8-1.fna.fbcdn.net/v/t1.0-9/76678962_2275468072745123_175326547912163328_n.jpg?_nc_cat=110&_nc_oc=AQmYCVd5HRkEVBCfUJG3qRCRkM7oi-FPdLj9142nIGJ5-pspVWErk3fg0XPamekwZN4&_nc_ht=scontent.fsgn8-1.fna&oh=ca2d608b15d3f6a63465aadf0758edc7&oe=5E498C83"width=400></center>
<center> Hình 7: Mô phỏng phép tịnh tiến vector [2,1] trên hệ trục tọa độ Oxy [2]
</p>

### c. Phép biến đổi Euclidean

Trước khi tìm hiểu phép biến đổi **Euclidean**, ta có câu hỏi như sau: *liệu ta có thể sử dụng hệ tọa độ đồng nhất cho các phép biến đổi tuyến tính ở phần đầu?* Đáp án là **có thể**. Ta có thể thay đổi ma trận $2$x$2$ thành ma trận $3$x$3$ cho các phép biến đổi tuyến tính một cách dễ dàng như sau:
$$
\def\arraystretch{2.0}
  
     \begin{array}
     {c:c:c}
     {Scale\\ \bold{M}=
     \begin{bmatrix}
     a & 0 & 0\\
   	 0 & b & 0\\
   	 0 & 0 & 1
     \end{bmatrix}}
     &
     {Flip\ Oy\\ \bold{M}=
    \begin{bmatrix}
     -1 & 0 & 0\\
   	 0 & -1 & 0\\
   	 0 & 0 & 1
     \end{bmatrix}}
    \\ \hline
    {Rotation\\ \bold{M}=
     \begin{bmatrix}
    \cos\theta & -\sin\theta & 0\\
  \sin\theta &  \cos\theta & 0 \\
  0 & 0 & 1
     \end{bmatrix}}
     &    
     {Flip\  Ox\\ \bold{M}=
    \begin{bmatrix}
     -1 & 0 & 0\\
   	 0 & 1 & 0\\
   	 0 & 0 & 1
     \end{bmatrix}}
    \\ \hline
     {Shear\\ \bold{M}=
     \begin{bmatrix}
  	1 & a & 0\\
  	b & 1 & 0\\
  	0 & 0 & 1
     \end{bmatrix}} & 
   
   {Identity\\ \bold{M}=
    \begin{bmatrix}
     1 & 0 & 0\\
   	 0 & 1 & 0\\
   	 0 & 0 & 1
     \end{bmatrix}}
     
  \end{array}
$$
Một câu hỏi nữa được đặt ra: *sẽ như thế nào nếu ta kết hợp các phép biến đổi tuyến tính với nhau? và kết hợp các phép biến đổi tuyến tính với phép biến đổi phép tịnh tiến?*

Đó chính là ý tưởng phép biến đổi Euclidean: **kết hợp giữa phép xoay và phép tịnh tiến.**

Tuy nhiên, *làm cách nào để kết hợp giữa xoay và tịnh tiến?* Chỉ cần **nhân** 2 ma trận của 2 phép biến đổi xoay và tịnh tiến với nhau, ta sẽ được ma trận Euclidean tương ứng. *Vậy thứ tự nhân 2 ma trận có ảnh hưởng hay không?* Nhìn vào Hình 8 ta sẽ thấy được đáp án: ***Thứ tự các phép biến đổi khác nhau sẽ cho kết quả khác nhau***.

<p>
    <center><img src="https://scontent.fsgn4-1.fna.fbcdn.net/v/t1.0-9/78955029_2286757038282893_5045303309586399232_n.jpg?_nc_cat=101&_nc_ohc=ex1GwE3l9vsAQli8X3e6jd4pS4sfx8lCaWGpwlcA-QkKfTtGaW5eg_cuA&_nc_ht=scontent.fsgn4-1.fna&oh=d09e6b5cb8568704495e7dca50231c2a&oe=5E46C785"width=600></center>
<center> Hình 8: Minh họa sự khác nhau khi thay đổi thứ tự các phép biến đổi [4]
</p>

Tóm lại ta có ma trận $3$x$3$ để biến đổi Euclidean:

<center>
   Euclidean = Translation + Rotation  
</center>

$$
\begin{bmatrix}
\cos\theta & -\sin\theta & t_x\\
\sin\theta &  \cos\theta & t_y\\
0 & 0 & 1
\end{bmatrix}
$$
<p>
    <center><img src="https://github.com/nguyenphan99/Geometric_Transformation/blob/master/images/rose_euclidean.gif?raw=true"width=400></center>
<center> Hình 9: Minh họa phép Euclidean
</p>

### d. Phép đồng dạng - Similarity transformation

Tương tự như Euclidean, **phép đồng dạng là sự kết hợp giữa Euclidean và tỉ lệ đều** - uniform scale.

Ta có thể tìm được ma trận biến đổi dễ dàng bằng mẹo lấy ma trận biến đổi của Euclidean, rồi lấy các phần tử trong ma trận vừa được mà có liên quan đến phép tỉ lệ để nhân với tỉ lệ $s$ :

<center>
   Similarity = Rotation + Translation + Uniform Scale
</center>

$$
\begin{bmatrix}
s\cos\theta & s(-\sin\theta) & t_x\\
s\sin\theta &  s\cos\theta & t_y\\
0 & 0 & 1
\end{bmatrix}
$$
Hình 10 minh họa một phép đồng dạng trong thực tế. So với Hình 9 thì phép tương tự sẽ có thêm sự thay đổi đều về tỉ lệ.

<p>
    <center><img src="https://github.com/nguyenphan99/Geometric_Transformation/blob/master/images/rose_similarity.gif?raw=true"width=400></center>
<center> Hình 10: Minh họa phép đồng dạng
</p>

### e. Biến đổi Affine - Affine transformation

Phép biến đổi Affine là **sự kết hợp giữa phép đồng dạng và phép trượt nghiêng**.

Ta có thể nhận được ma trận biến đổi bằng cách tương tự như ở phép đồng dạng. Sau cùng ta được ma trận biến đổi Affine:
<center>
    Affine = Rotation + Translation + Unform Scale + Shear
</center>

$$
\begin{bmatrix}
s\cos\theta - bs\sin\theta & -s\sin\theta + as\cos\theta & t_x\\
s\sin\theta + bs\cos\theta &  s\cos\theta+as\sin\theta & t_y \\
0 & 0 & 1
\end{bmatrix}
$$
<p>
    <center><img src="https://github.com/nguyenphan99/Geometric_Transformation/blob/master/images/rose_affine.gif?raw=true"width=400></center>
<center> Hình 11: Minh họa phép affine
</p>

Các tính chất của phép biến đổi Affine:

* *Gốc tọa độ có thể thay đổi*
* Đường thẳng ánh xạ thành đường thẳng
* Bảo toàn tính song song
* Tỉ lệ được giữ nguyên
* Bảo toàn tổ hợp biến đổi chính nó (tổ hợp của các phép biến đổi Affine cũng là Affine)

### d. Biến đổi phối cảnh - Projective transformation

Tương tự ý tưởng với phép biến đổi Affine, Biến đổi phối cảnh là sự kết hợp giữa biến đổi Affine và Projective Warp (bẻ cong phép chiếu - tạm dịch). Vấn đề này sẽ được trình bày trong bài tiếp theo.

Các tính chất của phép biến đổi phối cảnh:

* *Gốc tọa độ có thể thay đổi*
* Đường thẳng ánh xạ thành đường thẳng
* *Tính song song có thể không được bảo toàn*
* *Tỉ lệ bị thay đổi*
* Bảo toàn tổ hợp biến đổi chính nó (tổ hợp của các phép biến đổi phối cảnh cũng là phối cảnh)

Dưới đây là bảng tổng hợp so sánh tính chất của các phép biến đổi hình học:

|              Tuyến tính               |               Tịnh tiến               |               Euclidean               |               Đồng dạng               |                Affine                 |                    Phối cảnh                     |
| :-----------------------------------: | :-----------------------------------: | :-----------------------------------: | :-----------------------------------: | :-----------------------------------: | :----------------------------------------------: |
|     ***Gốc tọa độ giữ  nguyên***      |         Gốc  tọa độ thay đổi          |         Gốc  tọa độ thay đổi          |         Gốc  tọa độ thay đổi          |         Gốc  tọa độ thay đổi          |               Gốc  tọa độ thay đổi               |
| Đường  thẳng ánh xạ thành đường thẳng | Đường  thẳng ánh xạ thành đường thẳng | Đường  thẳng ánh xạ thành đường thẳng | Đường  thẳng ánh xạ thành đường thẳng | Đường  thẳng ánh xạ thành đường thẳng |      Đường  thẳng ánh xạ thành đường thẳng       |
|       Bảo  toàn tính song song        |       Bảo  toàn tính song song        |       Bảo  toàn tính song song        |       Bảo  toàn tính song song        |       Bảo  toàn tính song song        | ***Tính song song có thể  không được bảo toàn*** |
|         Tỉ  lệ được bảo toàn          |         Tỉ  lệ được bảo toàn          |         Tỉ  lệ được bảo toàn          |         Tỉ  lệ được bảo toàn          |         Tỉ  lệ được bảo toàn          |         ***Tỉ lệ không được bảo  toàn***         |
|  Bảo  toàn tổ hợp biến đổi chính nó   |  Bảo  toàn tổ hợp biến đổi chính nó   |  Bảo  toàn tổ hợp biến đổi chính nó   |  Bảo  toàn tổ hợp biến đổi chính nó   |  Bảo  toàn tổ hợp biến đổi chính nó   |        Bảo  toàn tổ hợp biến đổi chính nó        |

## 4. Ứng dụng và tham khảo.

### a. Ứng dụng

Biến đổi hình học là tiền đề cho *bài toán phát hiện vật thể* mà ứng dụng quen thuộc nhất là ảnh Panorama như Hình 12:

<p>
    <center><img src="https://scontent.fsgn1-1.fna.fbcdn.net/v/t1.0-9/78393869_2288402194785044_5177303975468204032_o.jpg?_nc_cat=100&_nc_ohc=G6vSd6cuBe8AQl3HB0-JFuceRHCG5hjB9zx0Yyp05AhKW_ok6QA1pc79g&_nc_ht=scontent.fsgn1-1.fna&oh=1cd117ed591e997ff17f309c8662e8e2&oe=5E8CBD5D"></center>
<center> Hình 12: Panorama là một ngữ cảnh ứng dụng của phép biến đổi hình học
</p>

### b. Tham khảo

Có thể tham khảo code:  https://github.com/nguyenphan99/Geometric_Transformation/blob/master/transform.py 

[1] 16-385 Computer Vision, Spring 2019:  http://www.cs.cmu.edu/~16385/ 

[2]  Đại học Carnegie Mellon: http://www.cs.cmu.edu/~16385/lectures/lecture7.pdf 

[3]  Đại học Michigan Technological: https://pages.mtu.edu/~shene/COURSES/cs3621/NOTES/geometry/geo-tran.html 

[4]  Đại học Bách Khoa Hà Nội: https://users.soict.hust.edu.vn/trungtt/uploads/slides/CG05_ModelingTrans.pdf 

[5]  Wikipedia:  https://en.wikipedia.org/wiki/Geometric_transformation 





