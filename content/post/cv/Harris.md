---
title: "Harris: Thuật toán phát hiện góc"
date: 2019-11-12T13:34:08+07:00
draft: false
authors: ["tuyenhs"]
categories:
- computer vision
tags:
- harris
- vision
slug: vietcs/post/cv/Harris
---

<img src='https://scontent.fsgn3-1.fna.fbcdn.net/v/t1.0-9/74698890_1305517486297127_1169934286678130688_n.jpg?_nc_cat=111&_nc_oc=AQmdKz1I6D-shTzsXioDqVmq38whl-T-7DtaHVquhDvyHoHvTfClDRQWDPyBiykgJhk&_nc_ht=scontent.fsgn3-1.fna&oh=666cd336bbfdbd941a15ecc9e9fb8f2a&oe=5E41B75A'>

### 1. Vì sao ta cần phát hiện góc?

#### 1.1. Mỗi ảnh đều có những điểm nổi bật riêng.

Chụp ảnh toàn cảnh (Panorama) đã trở nên rất quen thuộc trong nhiếp ảnh. Ở đó máy tính có nhiệm vụ ghép nhiều ảnh ở các góp chụp khác nhau một cách tự nhiên nhất. Nếu bạn phải thay thế máy tính làm công việc này, bạn sẽ ghép hai tấm ảnh sau như thế nào?

<p>
    <img src='https://scontent.fsgn4-1.fna.fbcdn.net/v/t1.0-9/72176098_1306438322871710_2361752561305780224_n.jpg?_nc_cat=103&_nc_oc=AQk5084m16LZ6FBfX9iuI9qdhnhGkjenL4RyE1Wm3Sf--ReDJF5ffiLXiC5YQg9FVxk&_nc_ht=scontent.fsgn4-1.fna&oh=a39e94366e54df53837039aefc52dc47&oe=5E54E3F2'>
    <center><caption>Hình 1: Bạn sẽ chọn điểm nào?</caption></center>
</p>

Bạn sẽ chọn điểm A trên bầu trời trong xanh? Đương nhiên không rồi :grin: Ta sẽ phải chọn những điểm "nổi bật" của mỗi tấm ảnh chẳng hạn B hay C là những đỉnh núi. Bạn sẽ nhận thấy rằng dù nhìn xa hay nhìn gần,  nhìn ở điều kiện nào đi chăng nữa những điểm này cũng dễ dàng được nhận ra. Điểm B hay C thỏa mãn đặc điểm mang **nhiều thông tin**, **riêng biệt** so với các điểm còn lại và **bất biến** (với các điều kiện như góc chụp, ánh sáng,...).

Những điểm như vậy được gọi là điểm "nổi bật" (**interest points**) của ảnh. Và góc (corner) cũng là một loại điểm "nổi bật". Trong bài hôm nay, mình xin được giới thiệu thuật toán phát hiện góc Harris [2] được giới thiệu vào năm 1988 bởi Chris Harris and Mike Stephens, đây là một trong những thuật toán giải quyết vấn đề phát hiện góc trong ảnh.

#### 1.2. Ứng dụng của bài toán phát hiện góc.

Việc phát hiện những điểm "nổi bật" nói chung hay góc nói riêng có ứng dụng rất lớn không chỉ trong chụp ảnh toàn cảnh mà còn ứng dụng trong bài toán:

- Nhận diện ở mức thực thể (Instance Regconition)
- Điều hướng người máy (Robot Navigation)
- Xây dựng mô hình 3D (3D Reconstruction)
- Theo vết hành động (Motion Tracking)
- ... 

<p>
	<img src='https://www.robots.ox.ac.uk/~vgg/practicals/instance-recognition/images/cover.png' width=500>
    <center><caption>Hình 2: Phát hiện các góc giúp ta dễ dàng nhận diện đây là tòa nhà ở trường Souls thuộc đại học Oxford (Bài toán nhận diện ở mức thực thể) [1]</caption></center>
</p>

### 2. Thuật toán phát hiện góc Harris là gì?

#### 2.1. Đặc điểm của góc trong ảnh.

Giả sử ta có một tấm ảnh và một cửa sổ (màu xanh), lần lượt trượt cửa sổ này theo nhiều hướng trên toàn bộ tấm ảnh. Ta nhận thấy ở vùng phẳng (hình đầu tiên) khung xanh không thay đổi dù ta trượt của sổ này ở bất kỳ hướng nào. Ở vùng biên cạnh dọc (hình thứ hai) khung xanh không thay đổi khi ta trượt theo trục dọc, chỉ thay đổi khi trượt theo trục ngang. Ở vùng góc (hình thứ ba) dù ta trượt theo bất kỳ hướng nào, khung xanh đều thay đổi một cách đáng kể.

<p>
    <img src='http://jieyanghu.com/wp-content/uploads/2015/09/harriswindow.jpg' width=800>
    <center><caption>Hình 3: Đặc điểm của vùng phẳng, biên cạnh và góc [3]</caption></center>
</p>

#### 2.2. Một chút toán học về thuật toán Harris

Phần này giải thích ý nghĩa công thức của thuật toán, bạn có thể **bỏ qua và đi thẳng đến phần 3** nếu không quan trọng việc hình thành công thức. 

Lợi dụng đặc điểm của góc trong ảnh, ta tạo ra một cửa sổ trượt trên ảnh nếu cửa sổ này thay đổi đáng kể khi trượt theo bất kỳ hướng nào sẽ được xem là góc. 

Vì vậy, vấn đề của ta làm cho hàm tối ưu sau lớn:
$$
E(u, v) = \sum_{x,y \in W} {(I(x+u, y+v) - I(x, y))^2} \quad (1)
$$
Với $I$ là ma trận ảnh (tương ứng vùng ảnh xám ở hình 3), $W$ là ma trận cửa sổ trượt (tương ứng cửa sổ xanh ở hình 3), $[u, v]$ là vector trượt của cửa sổ $W$ (tương ứng một vector hướng quay xung quanh cửa sổ xanh ở hình 3), $x$ và $y$ tương ứng là tọa độ điểm ảnh thuộc cửa sổ $W$ đang xét. Công thức (1) tính tổng bình phương độ lỗi của hai cửa sổ $W$ khi trượt một vector  $[u, v]$ trên ảnh $I$. Tuy nhiên  $I(x+u, v+y)$ mất nhiều thời gian khi tính trực tiếp nên ta dùng phương pháp chuỗi Taylor [4] để tính xấp xỉ thông qua đạo hàm:
$$
I(x+u, y+v) \approx I(x, y) + \frac{\partial I}{\partial x}u + \frac{\partial I}{\partial y}v \quad (2)
$$
Thay (2) vào (1), rút gọn rồi khai triển bình phương ta thu được:
$$
E(u, v) \approx \sum_{x,y \in W} {Au^2 + 2Buv + Cv^2} \quad (3)
$$

$$
A = \frac{\partial I}{\partial x}, B = \frac{\partial I}{\partial x}\frac{\partial I}{\partial y}, C = \frac{\partial I}{\partial y} \quad
$$

Từ (3) ta có thể viết $E(u, v)$ theo góc nhìn ma trận (bạn không tin có thể nhân các phần tử (4) sẽ thu được (3) ):
$$
E(u, v) \approx  \begin{bmatrix}u&v\end{bmatrix} \begin{bmatrix}A&B\\C&D\end{bmatrix} \begin{bmatrix}u\\v\end{bmatrix} \quad (4)
$$
$$
Xem \quad M = \begin{bmatrix}A&B\\B&C\end{bmatrix}
$$

Ma trận $M$ cho ta biết phân phối đạo hàm (distribution of gradients) xung quanh một điểm ảnh. Nếu phân tích ma trận $M$ ta sẽ thu được hai trị riêng $\lambda_{max}$ và $\lambda_{min}$ tương ứng với hướng thay đổi lớn nhất và hướng thay đổi ít nhất khi dịch chuyển cửa sổ theo vector  $[u, v]$. Cái ta mong muốn là làm $E(u, v)$ càng lớn càng tốt tức phải thay đổi lớn ở tất cả các hướng... hay cụ thể hơn $\lambda_{max}$ > $\lambda_{min}$ và $\lambda_{min}$ phải lớn.

Đến đây có nhiều cách để xác định góc theo $\lambda_{max}$ và $\lambda_{min}$. Tác giả đã đề ra công thức Harris response calculation sau:
$$
f = \frac{\lambda_{max} \lambda_{min}}{\lambda_{max}+\lambda_{min}} \quad (5)
$$
Theo công thức [5] để $f$ càng lớn thì cả $\lambda_{max}$ và $\lambda_{min}$ đều lớn và điều này thõa mãn tính chất góc mà ta đã phân tích ở **mục 2.1** 

<p>
    <img src='https://www.meccanismocomplesso.org/wp-content/uploads/2016/12/harris-corner-detection-eigen-values-diagram.jpg' width=400>
    <center><caption>Tương quan giữa hai trị riêng đến việc phát hiện góc [5]</caption></center>
</p>

Ngoài ra, trong thực tế có một công thức khác để tính toán $f$ thay thế (5) hay được sử dụng:
$$
f = \lambda_{max}\lambda_{min} - k(\lambda_{max} + \lambda_{min})^2 \quad (6)
$$
Trong đó thực nghiệm chứng minh $k$ thuộc khoảng $[0.04, 0.06]$ cho kết quả tốt nhất và:
$$
\lambda_{max}\lambda_{min} = det(M) = AC - B^2
$$

$$
\lambda_{max}+\lambda_{min} = trace(M) = A + C
$$

### 3. Cài đặt thuật toán phát hiện góc Harris

- **Bước 1:** Tính toán đạo ảnh $I_x$ (theo trục $O_x$) và $I_y$ (theo trục $O_y$) . Ở đây dùng bộ lọc Sobel [7] để tìm đạo hàm cho ảnh.

```python
ix = cv2.Sobel(img, cv2.CV_64F, 1, 0, ksize=3)
iy = cv2.Sobel(img, cv2.CV_64F, 0, 1, ksize=3)
```

- **Bước 2:** Tính A, B, C trong ma trận $M$ tương ứng $$I_x^2, I_{x,y}, I_y^2$$ 

```python
ix2 = ix * ix
iy2 = iy * iy
ixy = ix * iy
```

Lưu ý, phép nhân ở đây là phép nhân từng phần tử với nhau chứ không phải phép nhân ma trận.

- **Bước 3:** Lọc nhiễu $$I_x^2, I_{x,y}, I_y^2$$ bằng bộ lọc (ở đây dùng Gaussian [8])

```python
ix2 = cv2.GaussianBlur(ix2, (3,3), 0)
iy2 = cv2.GaussianBlur(iy2, (3,3), 0)
ixy = cv2.GaussianBlur(ixy, (3,3), 0)
```

- **Bước 4:** Tính ma trận $r$ là tập hợp điểm $f$ cho mỗi điểm ảnh

```python
for i in range(img.shape[0]):
    for j in range(img.shape[1]):
        m = np.array([[ix2[i, j], ixy[i, j]], [ixy[i, j], iy2[i, j]]],
                     dtype=np.float64)
        r[i, j] = np.linalg.det(m) - 0.04 * (np.power(np.trace(m), 2))
```

- **Bước 5:** Phát hiện góc
  - Bước 5.1: Chọn ngưỡng và phát hiện góc nếu $r$ > ngưỡng (ở đây là $0.01 * r_{max}$)
  - Bước 5.2: Đây là bước chọn điểm có $r$ lớn nhất cục bộ (còn gọi là non-maximum suppression) với mục tiêu phát hiện những điểm góc quá gần nhau. Chọn theo vùng cửa sổ là một giải pháp ở bước này. Ở đây ta chọn vùng cửa sổ kích thước $3$ x $3$, nếu trong vùng này có nhiều hơn một điểm thỏa mãn ngưỡng thì chọn điểm có giá trị $r$ lớn nhất. 

```python
for i in range(img.shape[0] - 1):
    for j in range(img.shape[1] - 1):
        if r[i, j] > 0.01 * rmax \
        	and r[i, j] > r[i-1, j-1] \
            and r[i, j] > r[i-1, j+1] \
            and r[i, j] > r[i+1, j-1] \
            and r[i, j] > r[i+1, j+1]:
            result[i, j] = 1
```

- Mã giã đầy đủ:

```python
# Thư viện
import numpy as np
import matplotlib.pyplot as plt
from PIL.Image import *
from scipy import ndimage
import cv2

# Hàm phát hiện góc bằng thuật toán Harris
def Harris(img):
    # Tính đạo hàm theo trục Ox và Oy
	ix = cv2.Sobel(img, cv2.CV_64F, 1, 0, ksize=3)
	iy = cv2.Sobel(img, cv2.CV_64F, 0, 1, ksize=3)
	
    # Tính các thành phần A, C, B
	ix2 = ix * ix
	iy2 = iy * iy
	ixy = ix * iy

    # Lọc nhiễu A, B, C bằng bộ lọc Gaussian
    ix2 = cv2.GaussianBlur(ix2, (3,3), 0)
    iy2 = cv2.GaussianBlur(iy2, (3,3), 0)
    ixy = cv2.GaussianBlur(ixy, (3,3), 0)

	result = np.zeros((img.shape[0], img.shape[1]))
	r = np.zeros((img.shape[0], img.shape[1]))
	rmax = 0

    # Tìm ma trận r gồm điểm f của từng pixel 
	for i in range(img.shape[0]):
		for j in range(img.shape[1]):
			m = np.array([[ix2[i, j], ixy[i, j]], [ixy[i, j], iy2[i, j]]], dtype=np.float64)
			r[i, j] = np.linalg.det(m) - 0.04 * (np.power(np.trace(m), 2))
			if r[i, j] > rmax:
				rmax = r[i, j]
	
    # Trực quan hóa việc phát hiện góc với ngưỡng bằng 0.01*max[r] với khung cửa sổ 3x3
	for i in range(img.shape[0] - 1):
		for j in range(img.shape[1] - 1):
			if r[i, j] > 0.01 * rmax \
            and r[i, j] > r[i-1, j-1] \
            and r[i, j] > r[i-1, j+1] \
            and r[i, j] > r[i+1, j-1] \
            and r[i, j] > r[i+1, j+1]:
				result[i, j] = 1

	pc, pr = np.where(result == 1)
	plt.plot(pr, pc, 'rp')
	plt.imshow(im, 'gray')
	plt.show()


im = cv2.imread('đường dẫn tới ảnh')
gray = cv2.cvtColor(im, cv2.COLOR_BGR2GRAY).astype('float64')
Harris(gray)
```

- Kết quả:

Để kiểm tra tính chính xác của quá trình cài đặt thuật toán, ta sẽ so sánh kết quả khi chạy mã giã trên với hàm Harris có sẵn của thư viện OpenCV.

<img src='https://scontent.fsgn3-1.fna.fbcdn.net/v/t1.0-9/74698890_1305517486297127_1169934286678130688_n.jpg?_nc_cat=111&_nc_oc=AQmdKz1I6D-shTzsXioDqVmq38whl-T-7DtaHVquhDvyHoHvTfClDRQWDPyBiykgJhk&_nc_ht=scontent.fsgn3-1.fna&oh=666cd336bbfdbd941a15ecc9e9fb8f2a&oe=5E41B75A'>

<img src='https://scontent.fsgn8-1.fna.fbcdn.net/v/t1.0-9/75474232_1303473896501486_7125238778561560576_n.jpg?_nc_cat=110&_nc_oc=AQnQGt9dQzA9ELpC2wtXwUgpSN-6Fte43a83a6U7PJJwvUFQRyW0Au_aFOKoms7xOqg&_nc_ht=scontent.fsgn8-1.fna&oh=b7c57da16f2b7efb3fd3bf32d7e76270&oe=5E51A7E5'>

### 4. Tham khảo

[1] http://www.robots.ox.ac.uk/~vgg/practicals/instance-recognition/index.html

[2] https://en.wikipedia.org/wiki/Harris_Corner_Detector

[3] https://www.researchgate.net/figure/Figure2-Harris-Corner-Detector-Basic-Idea-13_fig1_321757491

[4] https://vi.wikipedia.org/wiki/Chuoi_Taylor

[5] https://www.meccanismocomplesso.org/en/opencv-python-harris-corner-detection-a-method-to-detect-corners-in-an-image/

[6] https://stackoverflow.com/questions/3862225/implementing-a-harris-corner-detector/22990011#22990011

[7] https://en.wikipedia.org/wiki/Gaussian_blur

[8] https://en.wikipedia.org/wiki/Sobel_operator