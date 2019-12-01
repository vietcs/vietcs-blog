---
title: "Nhận diện cạnh Harris Corner"
date: 2019-10-13T13:34:08+07:00
draft: false
authors: ["tuyenhs"]
categories:
  - computer vision
tags:
  - introductory
  - vision
  - classical
slug: nhan-dien-canh-harris-corner
---



# 1. Vì sao ta cần phát hiện góc?

 1.1. Mỗi ảnh đều có những điểm nổi bật riêng.

Chụp ảnh toàn cảnh (Panorama) đã trở nên rất quen thuật trong nhiếp ảnh. Ở đó máy tính có nhiệm vụ ghép nhiều ảnh ở các góp chụp khác nhau một cách tự nhiên nhất. Nếu bạn phải thay thế máy tính làm công việc này, bạn sẽ ghép hai tấm ảnh sau như thế nào?

<p>
    <img src='https://miro.medium.com/max/956/1*j0NYYfC-b7i5KjGOhFqnPg.png'>
    <center><caption></caption></center>
</p>

Bạn sẽ chọn những điểm trên bầu trời trong xanh hay những điểm giữa nền tuyết trắng xóa? Đương nhiên không rồi :grin: Ta sẽ phải chọn những điểm "nổi bật" của mỗi tấm ảnh chẳng hạn các đỉnh núi. Những điểm này phải thỏa mãn đặc điểm mang **nhiều thông tin**, **riêng biệt** so với các điểm còn lại và **bất biến** (với các điều kiện như góc chụp, ánh sáng,...).

Những điểm như vậy được gọi là điểm "nổi bật" (**interest points**) của ảnh. Và góc (corner) cũng là một loại điểm "nổi bật".

## 1.2. Ứng dụng của bài toán phát hiện góc.

Việc phát hiện những điểm "nổi bật" nói chung hay góc nói riêng có ứng dụng rất lớn không chỉ trong chụp ảnh toàn cảnh mà còn ứng dụng trong bài toán:

- Nhận diện ở mức thực thể (Instance Regconition)
- Điều hướng người máy (Robot Navigation)
- Xây dựng mô hình 3D (3D Reconstruction)
- Theo vết hành động (Motion Tracking)
- ... 

<p>
	<img src='http://www.robots.ox.ac.uk/~vgg/practicals/instance-recognition/images/cover.png' width=500>
    <center><caption>Bài toán nhận diện ở mức thực thể (Instance Regconition) [1]</caption></center>
</p>

# 2. Thuật toán phát hiện góc Harris là gì?

Thuật toán phát hiện góc Harris [2] được giới thiệu vào năm 1988 bởi Chris Harris and Mike Stephens, đây là một trong những thuật toán giải quyết vấn đề phát hiện góc trong ảnh.

## 2.1. Đặc điểm của góc trong ảnh.

Giả sử ta có một tấm ảnh và một cửa sổ (màu xanh), lần lượt trượt cửa sổ này theo nhiều hướng trên toàn bộ tấm ảnh. Ta nhận thấy ở vùng phẳng (hình đầu tiên) khung xanh không thay đổi dù ta trượt của sổ này ở bất kỳ hướng nào. Ở vùng biên cạnh dọc (hình thứ hai) khung xanh không thay đổi khi ta trượt theo trục dọc, chỉ thay đổi khi trượt theo trục ngang. Ở vùng góc (hình thứ ba) dù ta trượt theo bất kỳ hướng nào, khung xanh đều thay đổi một cách đáng kể.

<p>
    <img src='https://www.researchgate.net/profile/Faisel_Mohammed/publication/321757491/figure/fig1/AS:570839621353472@1513110179045/Figure2-Harris-Corner-Detector-Basic-Idea-13.png'>
    <center><caption>Đặc điểm của vùng phẳng, biên cạnh và góc [3]</caption></center>
</p>

## 2.2. Một chút toán học về thuật toán Harris

Phần này giải thích ý nghĩa công thức của thuật toán, bạn có thể **bỏ qua và đi thẳng đến phần 3** nếu không quan trọng việc hình thành công thức. 

Lợi dụng đặc điểm của góc trong ảnh, ta tạo ra một cửa sổ trượt trên ảnh nếu cửa sổ này thay đổi đáng kể khi trượt theo bất kỳ hướng nào sẽ được xem là góc. 

Vì vậy, vấn đề của ta làm cho hàm tối ưu sau lớn:
$$
E(u, v) = \sum_{x,y \in W} {(I(x+u, y+v) - I(x, y))^2} \quad (1)
$$
Với $I$ là ma trận ảnh, $W$ là ma trận cửa sổ trượt, $[u, v]$ là vector trượt của cửa sổ W. Công thức (1) tính tổng bình phương độ lỗi của hai cửa sổ $W$ khi trượt một vector  $[u, v]$ trên ảnh $I$. Tuy nhiên  $I(x+u, v+y)$ mất nhiều thời gian khi tính trực tiếp nên ta dùng phương pháp chuỗi Taylor [4] để tính xấp xỉ thông qua đạo hàm:
$$
I(x+u, y+v) = I(x, y) + \frac{\partial I}{\partial x}u + \frac{\partial I}{\partial y}v \quad (2)
$$


Thay (2) vào (1), rút gọn rồi khai triển bình phương ta thu được:


$$
E(u, v) = \sum_{x,y \in W} {Au^2 + 2Buv + Cv^2} \quad (3)
$$

$$
A = \frac{\partial I}{\partial x}, B = \frac{\partial I}{\partial x}\frac{\partial I}{\partial y}, C = \frac{\partial I}{\partial y} \quad
$$

Từ (3) ta có thể viết $E(u, v)$ theo góc nhìn ma trận (bạn không tin có thể nhân các phần tử (4) sẽ thu được (3) ):
$$
E(u, v)=  \begin{bmatrix}u&v\end{bmatrix} \begin{bmatrix}A&B\\\C&D\end{bmatrix} \begin{bmatrix}u\\\v\end{bmatrix} \quad (4)
$$
$$
\text{Xem} \quad M = \begin{bmatrix}A&B\\\B&C\end{bmatrix}
$$

Ma trận $M$ cho ta biết phân phối đạo hàm (distribution of gradients) xung quanh một điểm ảnh. Nếu phân tích ma trận $M$ ta sẽ thu được hai trị riêng $ \lambda\_{max} $ và $\lambda\_{min}$ tương ứng với hướng thay đổi lớn nhất và hướng thay đổi ít nhất khi dịch chuyển cửa sổ theo vector  $[u, v]$. Cái ta mong muốn là làm $E(u, v)$ càng lớn càng tốt tức phải thay đổi lớn ở tất cả các hướng... hay cụ thể hơn $\lambda\_{max} > \lambda\_{min}$ và $\lambda\_{min}$ phải lớn.

Đến đây có nhiều cách để xác định góc theo $\lambda\_{max}$ và $\lambda\_{min}$. Tác giả đã đề ra công thức sau:
$$
f = \frac{\lambda\_{max} \lambda\_{min}}{\lambda\_{max}+\lambda\_{min}} \quad (5)
$$
Theo công thức [5] để $f$ càng lớn thì cả $\lambda\_{max}$ và $\lambda\_{min}$ đều lớn và điều này thõa mãn tính chất góc mà ta đã phân tích ở **mục 2.1** 

<p>
    <img src='https://www.meccanismocomplesso.org/wp-content/uploads/2016/12/harris-corner-detection-eigen-values-diagram.jpg' width=400>
    <center><caption>Tương quan giữa hai trị riêng đến việc phát hiện góc [5]</caption></center>
</p>



Ngoài ra, trong thực tế có một công thức khác để tính toán $f$ thay thế (5) hay được sử dụng:
$$
f = \lambda\_{max}\lambda_{min} - k(\lambda\_{max} + \lambda\_{min})^2 \quad (6)
$$
Trong đó thực nghiệm chứng minh $k$ thuộc khoảng $[0.04, 0.06]$ cho kết quả tốt nhất và:
$$
\lambda\_{max}\lambda\_{min} = det(M) = AC - B^2
$$

$$
\lambda\_{max}+\lambda\_{min} = trace(M) = A + C
$$

# 3. Cài đặt thuật toán phát hiện góc Harris

- **Bước 1:** Tính toán đạo ảnh $I_x$ (theo trục $O_x$) và $I_y$ (theo trục $O_y$) . Ở đây dùng bộ lọc Sobel [7] để tìm đạo hàm cho ảnh.

```python
ix = ndimage.sobel(img, 0)
iy = ndimage.sobel(img, 1)
```

- **Bước 2:** Tính A, B, C trong ma trận M tương ứng $$I\_x^2, I\_{x,y}, I\_y^2$$ 

```python
ix2 = ix * ix
iy2 = iy * iy
ixy = ix * iy
```

Lưu ý, phép nhân ở đây là phép nhân từng phần tử với nhau chứ không phải phép nhân ma trận.

- **Bước 3:** Lọc nhiễu $$I\_x^2, I\_{x,y}, I\_y^2$$ bằng bộ lọc (ở đây dùng Gaussian [8])

```python
ix2 = ndimage.gaussian_filter(ix2, sigma=2)
iy2 = ndimage.gaussian_filter(iy2, sigma=2)
ixy = ndimage.gaussian_filter(ixy, sigma=2)
```

- **Bước 4:** Tính ma trận r là tập hợp điểm $f$ cho mỗi điểm ảnh

```python
for i in range(img.shape[0]):
    for j in range(img.shape[1]):
        m = np.array([[ix2[i, j], ixy[i, j]], [ixy[i, j], iy2[i, j]]],
                     dtype=np.float64)
        r[i, j] = np.linalg.det(m) - 0.04 * (np.power(np.trace(m), 2))
```

- **Bước 5:** Chọn ngưỡng và phát hiện góc theo ma trận r (chọn điểm có r lớn nhất trong khung cửa sổ, ở đây là 3x3)

```python
for i in range(img.shape[0] - 1):
    for j in range(img.shape[1] - 1):
        if r[i, j] > 0.01 * rmax and r[i, j] > r[i-1, j-1] and r[i, j] > r[i-1, j+1]
        				and r[i, j] > r[i+1, j-1] and r[i, j] > r[i+1, j+1]:
            result[i, j] = 1
```

- Full code:

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
	ix = ndimage.sobel(img, 0)
	iy = ndimage.sobel(img, 1)
	
    # Tính các thành phần A, C, B
	ix2 = ix * ix
	iy2 = iy * iy
	ixy = ix * iy

    # Lọc nhiễu A, B, C bằng bộ lọc Gaussian
	ix2 = ndimage.gaussian_filter(ix2, sigma=2)
	iy2 = ndimage.gaussian_filter(iy2, sigma=2)
	ixy = ndimage.gaussian_filter(ixy, sigma=2)

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
			if r[i, j] > 0.01 * rmax and r[i, j] > r[i-1, j-1] and r[i, j] > r[i-1, j+1] and r[i, j] > r[i+1, j-1] and r[i, j] > r[i+1, j+1]:
		    	result[i, j] = 1

	pc, pr = np.where(result == 1)
	plt.plot(pr, pc, 'rp')
	plt.imshow(im, 'gray')
	plt.show()


im = cv2.imread('đường dẫn tới ảnh')
gray = cv2.cvtColor(im, cv2.COLOR_BGR2GRAY).astype('float64')
Harris(gray)
```



# 4. Tham khảo

[1] http://www.robots.ox.ac.uk/~vgg/practicals/instance-recognition/index.html

[2] https://en.wikipedia.org/wiki/Harris_Corner_Detector

[3] https://www.researchgate.net/figure/Figure2-Harris-Corner-Detector-Basic-Idea-13_fig1_321757491

[4] https://vi.wikipedia.org/wiki/Chuoi_Taylor

[5] https://www.meccanismocomplesso.org/en/opencv-python-harris-corner-detection-a-method-to-detect-corners-in-an-image/

[6] https://stackoverflow.com/questions/3862225/implementing-a-harris-corner-detector/22990011#22990011

[7] https://en.wikipedia.org/wiki/Gaussian_blur

[8] https://en.wikipedia.org/wiki/Sobel_operator