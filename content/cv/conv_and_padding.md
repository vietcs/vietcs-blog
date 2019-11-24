---
title: "Toán tử Convolution"
date: 2019-10-17T13:34:08+07:00
draft: false
authors: ["khoatranrb"]
categories:
  - computer vision
tags:
  - introductory
  - convolution
  - padding
slug: /home/khoa/Desktop/vietcs-blog/content/cv/conv_and_padding.md
---

Convolution là kỹ thuật quan trọng trong *xử lý ảnh* (**Image processing**), được sử dụng chính yếu trong các phép toán trên ảnh như: đạo hàm ảnh (**gradient image**), làm trơn ảnh (**blurring**), nhận diện viền (**edge detection**), trích xuất đặc trưng (**feature extraction**).

Bài viết này cung cấp cho bạn kiến thức cơ bản về *Convolution* và tính chất của nó.

## 1.  Giới thiệu về toán tử  (hệ thống) tuyến tính

* Một toán tử (hệ thống) $H$ được gọi là tuyến tính (*linear*) nếu thỏa mãn cả hai tính chất sau:

  * **Tính chất cộng:**
    $$
    H(u_1+u_2)=H(u_1)+H(u_2)
    \tag3
    $$
  * **Tính chất nhân:**
  
  $$
   H(\alpha u_1) = \alpha H(u_1)
    \tag3
  $$
  
  
  
  * Trong đó:
  
    * $u_1$, $u_2$ là các tham số
    
    * $\alpha$  là một số thực      
  
* Trong ***Tín hiệu và hệ thống***,  $u_1$ & $u_2$ được gọi là tín hiệu vào, $H$ được gọi là hàm biến đổi.

* ***Ví dụ :***

  * $H(x)=2x$  tuyến tính vì chúng ta dễ thấy:

    * $H(x1+x2)=2(x1+x2)=2x1+2x2=H(x1)+H(x2)$

      

    * $H(3x)=2.3x=3.2x=3H(x)$
  
    * Một trường hợp dễ gây hiểu lầm, đó là $H(x)=x+1$. Nhìn có vẻ tuyến tính nhưng thật ra không phải vậy. Ta sẽ xem xét nó dựa trên hai tính chất ở trên:
      $$
      \begin{align}
      H(x1+x2)&=x1+x2+1 \\\ &=(x1+1)+(x2+1)-1 \\\ &=H(x1)+H(x2)-1\\\ &\not=H(x1)+H(x2) \end{align}
      $$
      

* Trong bài (Nhập môn Filtering) có nhắc đến khái niệm *bộ lọc tuyến tính*, qua các tính chất ở trên, ta có thể định nghĩa: ***"Một bộ lọc tuyến tính là một bộ lọc mà giá trị các output pixel có quan hệ tuyến tính với các pixel lân cận của nó."***

## 2. Cross-correlation và convolution

### *2.1. Cross-correlation (tương quan chéo)*

* Bạn đọc nên tìm hiểu trước về *Correlation Filter* ở bài (Nhập môn Filtering)

* *Cross-correlation*:

  * Công thức với *kenel* có kích thước $(2k+1)\times(2k+1)$: 
    $$
    G[i,j] = \sum\_{u=-k}^{k}\sum\_{v=-k}^{k}H[u,v]F[i+u,j+v]
    \tag4
    $$
  * Ký hiệu:

  $$
  G = H \otimes F
  $$
  * *Cross-correlation* giúp tìm kiếm *sự tương quan* của *kernel* trên ảnh gốc. Bạn có thể thấy vùng pixel trên ảnh gốc càng *tương quan* với kernel thì giá trị output càng lớn.


### *2.2. Convolution (Tích chập)*

* *Convolution* là toán tử mà ta xoay kernel 180 độ *(flip over)* rồi áp dụng phép *correlation*. 

  <p>
      <img src='https://raw.githubusercontent.com/khoaxxx238/DOC/master/img/corrconv.png' width=500>
      <center><caption><i>Nguồn: Introduction in Computer Vision (Udacity)</i></caption></center>
  </p>

  
  
  * Công thức:
    $$
    G[i,j] = \sum\_{u=-k}^{k}\sum\_{v=-k}^{k}H[u,v]F[i-u,j-v]
    \tag5
    $$
    
  * Ký  hiệu:
    $$
    G = H \ast F
    $$
  
  * Giá trị *tâm kernel* được gọi là điểm neo (**anchor point**).

### *2.3. Convolution có gì hơn cross-correlation?*

* Cả *Convolution* và *cross-correlation* đều là **bộ lọc tuyến tính**.

* Sự khác biệt duy nhất mà chúng ta thấy được qua công thức trên là: *convolution* lật ngược kernel rồi mới thực hiện sliding window. Tuy nhiên chính điều này đã tạo nên tính chất riêng của *convolution* mà *cross-correlation* không có:
  
  * **Tính giao hoán**:
    $$
    f\ast g = g\ast h
    \tag7
    $$
    
  * **Tính kết hợp:**
    $$
    f\ast g\ast h = f\ast (g\ast h)
    \tag7
    $$
    
  * **Tính phân phối:** Điều này thể hiện rằng thay vì ta lấy ảnh gốc $I$ convolve với *kernel* $K1$, sau đó lấy ảnh kết quả convolve với $K2$ thì ta có thể thực hiện lấy *kernel* $K1$ convole với $K2$ thành 1 *kernel* nào đó, sau đó lấy *kernel* kết quả này áp dụng cho ảnh gốc $I$. Chính nhờ tính chất này mà khi thiết kế *kernel*, thay vì thiết kế nhiều phép convolve tuần tự ta có thể kết hợp chúng lại thành 1 *kernel* duy nhất.
    $$
    f\ast (g+h)=f\ast g +f\ast h
    \tag7
    $$
    
  * Chính **tính kết hợp** này giúp ta có thể tối ưu hóa độ phức tạp của phép *convolution* trong lập trình. Điều này sẽ được làm sáng tỏ ở ngay dưới đây.

### *2.4. Tối ưu hóa phép convolution*

* Giả sử bạn có một bức ảnh $I$ có kích thước $N \times N$ *pixels*, một *kernel* $W\times W$. Khi thực hiện *sliding window*, ở mỗi vị trí, chúng ta phải thực hiện $W\times W$ phép nhân rồi cộng chúng lại với nhau. Để thực hiện trên toàn bộ $I$, chúng ta phải tính $N\times N\times W\times W = N^2W^2$ phép nhân, một con số rất lớn. Câu hỏi đặt ra bây giờ là: *Làm thế nào để tối thiểu số lượng phép tính khi thực hiện convolution?* Tính kết hợp của nó sẽ giúp ta trả lời câu hỏi này.

* Nếu bạn muốn thực hiện *convolution* với *kernel* $H = \begin{bmatrix}2&1&3\\\ 4&2&6\\\2&1&3\end{bmatrix}$, thay vì dùng nguyên ma trận này, bạn có thể tách ra như sau:
  $$
  H = \begin{bmatrix}2&1&3\\\ 4&2&6\\\2&1&3\end{bmatrix} = c\ast r=\begin{bmatrix}1\\\2\\\1\end{bmatrix}\ast\begin{bmatrix}2&1&3\end{bmatrix}
  $$
  
* Do đó ta có:
  $$
  G = H\ast I = (c\ast r)\ast I = c\ast (r\ast I)
  $$

* Trong trường hợp tổng quát, với $H$ có kích thước $W\times W$ thì $c$ và $r$ lần lượt có kích thước $W\times 1$ và $1\times W$. Lúc này bạn có thể thực hiện hai lần phép *convolution* với *kernel* nhỏ hơn. Độ phức tạp bây giờ là $2WN^2<W^2N^2$ vì ta thường lấy $W\eqslantgtr 3$.

### *2.5. Áp dụng trong Python*

* Sau đây chúng ta sẽ thử áp dụng *convolution* và *cross-correlation* với *kernel* $H = \begin{bmatrix}-1&0&1\\\ -2&0&2\\\ -1&0&1\end{bmatrix}$ cho tấm hình:

  <p>
      <img src = 'https://raw.githubusercontent.com/khoaxxx238/DOC/master/img/cv0.png' width=240>
  </p>

* **Bước 1:** Khai báo thư viện, load ảnh và tạo ma trận bộ lọc. Ở đây mình dùng module [cv2.filter2D](https://opencv-python-tutroals.readthedocs.io/en/latest/py_tutorials/py_imgproc/py_filtering/py_filtering.html) cho phép *cross-correlation* và [scipy.ndimage.convolve](https://docs.scipy.org/doc/scipy/reference/generated/scipy.ndimage.convolve.html) cho phép *convolution*.

  ``` python
  import cv2
  import numpy as np
  from scipy import ndimage
  
  # Load ảnh
  img = cv2.imread('đường dẫn ảnh',0)
  
  # Khởi tạo ma trận bộ lọc
  kernel = np.asanyarray([-1,0,1,-2,0,2,-1,0,1]).reshape((3,3))
  ```

* **Bước 2:** Thực hiện *cross-correlation*

  ``` python
  corr = cv2.filter2D(img,-1, mat)
  ```

* **Bước 3:** Thực hiện *convovlution*

  ``` python
  # Chuyển ma trận ảnh về kiểu dữ liệu float
  imgFloat = img.astype(float)
  # Thực hiện convolution
  conv = ndimage.convolve(imgFoat,kernel)
  # Với output pixel < 0, đưa về giá trị 0
  conv = np.where(conv<0, 0, kernel)
  # Với output pixel > 255, đưa về giá trị 255
  conv = np.where(conv>255, 255, kernel)
  # Đưa ma trận về kiểu dữ liệu ban đầu 
  conv = conv.astype(np.uint8)
  ```

* Kết quả:

  <p>
      <img src='https://raw.githubusercontent.com/khoaxxx238/DOC/master/img/res3.5.png' width=600>
  </p>

* Full code các bạn có thể xem ở [đây](https://github.com/khoaxxx238/DOC/blob/master/code/conv.py)

## 3. Padding

* Khi bạn dùng bộ lọc $W\times W$ cho một ảnh có kích thước $N\times N$, ảnh đầu ra sẽ có kích thước $(N-W+1)\times (N-W+1)$.

  <p>
      <img src='https://raw.githubusercontent.com/khoaxxx238/DOC/master/img/git.gif' width=500>
      <center><caption><i>Nguồn: Glass Box</i></caption></center>
  </p>

* Nếu bạn muốn đầu ra là ảnh có cùng kích thước với ảnh ban đầu, bạn chỉ cần tạo vùng đệm (**pad**) xung quanh ảnh đó. Dưới đây là các kỹ thuật **padding** phổ biến:

### *3.1. Constant padding (đệm hằng số):*

* Giá trị *identify* của vùng đệm mang một giá trị $a$ duy nhất. Giá trị này phụ thuộc vào loại ảnh mà bạn muốn xử lý.

  <p>
      <img src='https://raw.githubusercontent.com/leduckhai/My-Data-Container/master/VietCS%20Blog/Nh%E1%BA%ADp%20m%C3%B4n%20Image%20filtering/Zero%20padding.png' width=300>
      <center><caption><i>Zero padding</i></caption></center>
  </p>

  

* Nếu $a=0$, ta có **zero padding**. Điểm mạnh của **zero padding** là nó không tạo thêm đặc trưng (*feature*) cho ảnh. Đó là lý do **zero padding** được dùng nhiều trong các mô hình **Deep Learning**.

* Ví dụ:

  <p>
      <img src='https://raw.githubusercontent.com/khoaxxx238/DOC/master/img/constpad.png' width=500>
      <center><caption><i>Zero padding</i></caption></center>
  </p>

  

### *3.2. Reflect padding (Đệm phản chiếu)*

* Trong phương thức này, vùng đệm được lấy đối xứng từ vùng rìa ảnh qua các cạnh.

  <p>
      <img src = "https://raw.githubusercontent.com/khoaxxx238/DOC/master/img/ref.png" width=300>
      <center><caption><i>Reflect padding</i></caption></center>
  
* Khi trích xuất đặc trưng (*extract feature*) bằng *convolution*, phần cạnh sẽ bị bỏ qua. **Reflect padding** giúp bạn tạo ra thông tin *"fake"* bù vào phần đó. Tuy nhiên điều này không đúng trong mọi hoàn cảnh. Trong một số trường hợp **reflect padding** phá vỡ đặc trưng vốn có của ảnh.

* **Reflect padding** thường được dùng khi bạn quan tâm tới *biểu đồ sáng* (**brightness histogram**) và *độ tương phản* (**contrast**) của ảnh.

* Ví dụ 1:

  <p>
          <img src = "https://raw.githubusercontent.com/khoaxxx238/DOC/master/img/reflpad.png" width=500>
      <center><caption><i>Reflect padding</i></caption></center>
  </p>

* Ví dụ 2:

  <p>
              <img src = "https://raw.githubusercontent.com/khoaxxx238/DOC/master/img/refpad.png" width=500>
      <center><caption><i>Reflect padding</i></caption></center>
  </p>

  

### *3.3. Nearest neighbor padding*

* **Nearest neighbor padding** sử dụng các *pixel* ngoài cùng cho vùng đệm.

  <p>
      <img src = 'https://raw.githubusercontent.com/khoaxxx238/DOC/master/img/nnp.png' width=200>
       <center><caption><i>Nearest neighbor padding</i></caption></center>

* Ví dụ:

  <P>
      <img src="https://raw.githubusercontent.com/khoaxxx238/DOC/master/img/nnpad.png" width=500>
      <center><caption><i>Nearest neighbor padding</i></caption></center>
  </p>

  

### *3.4. Wrap padding*

* Khó mà giải thích bằng lời về **wrap padding**, bạn có thể hiểu qua các ví dụ sau:

  <p>
      <img src = 'https://raw.githubusercontent.com/khoaxxx238/DOC/master/img/wrappad.png' width=500>
      <center><caption><i>Wrap padding</i></caption></center>

*Thư viện OpenCV hỗ trợ khá đầy đủ phần này, các bạn có thể tham khảo ở [đây](https://www.sharebook.site/2017/08/13/opencv-basic-images-operations/).*

**Tổng hợp các phương pháp padding ở trên:**

<p>
    <img src = 'https://raw.githubusercontent.com/khoaxxx238/DOC/master/img/padding.png' width=600>
</p>



## **Tham khảo:**

[1] [Introduction to Computer Vision | Udacity](https://classroom.udacity.com/courses/ud810)

[2] [Introduction to Computer Vision | Cornell](http://www.cs.cornell.edu/courses/cs4670/2018sp/)

[3] [Linearity | Wiki](https://en.wikipedia.org/wiki/Linearity)

[4] [Cross-correlation | Wiki](https://en.wikipedia.org/wiki/Cross-correlation)

[5] [Convolution | Wiki](https://en.wikipedia.org/wiki/Convolution)

[6] [Convolution | minhng.info](https://minhng.info/tutorials/xu-ly-anh-convolution-la-gi.html)

[7] [Tích chập | STDIO](https://www.stdio.vn/articles/phep-tich-chap-trong-xu-ly-anh-convolution-386)

[8] [Cross-correlation vs Convolution](https://glassboxmedicine.com/2019/07/26/convolution-vs-cross-correlation/)

[9] [Making border in OpenCV](https://www.sharebook.site/2017/08/13/opencv-basic-images-operations)

[10] [Why use symmetric padding?](https://www.quora.com/Why-do-we-need-symmetric-padding-method-to-calculate-the-boundary-value-while-we-are-extracting-feature-in-case-of-2D-medical-image)

[11] [Why use reflect padding?](https://www.reddit.com/r/learnmachinelearning/comments/8hetaz/can_someone_explain_to_me_what_reflection_padding/)

[12] [Biến đổi tuyến tính | Wiki]([https://vi.wikipedia.org/wiki/Bi%E1%BA%BFn_%C4%91%E1%BB%95i_tuy%E1%BA%BFn_t%C3%ADnh](https://vi.wikipedia.org/wiki/Biến_đổi_tuyến_tính))

