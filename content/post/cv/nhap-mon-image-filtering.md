---
title: "Nhập môn Image Filtering"
date: 2019-10-25T22:36:42+07:00
draft: false
authors: ["khaild"]
categories: 
  - computer vision
tags:
  - introductory
  - vision
  - image-processing
  - filter
  - template-matching
slug: nhap-mon-image-filtering
---

## 1. Giới thiệu
Filter (hay tiếng Việt là bộ lọc) được dùng rất nhiều trong thị giác máy tính, nhằm loại bỏ nhiễu (**denoising**), làm mờ ảnh (**blurring**), làm mượt (**smoothing**), cân bằng các giá trị pixel trong ảnh (**equalization**), nhận diện viền (**edge detection**), trích xuất đặc trưng (**feature extraction**), so khớp mẫu (**template matching**),... thông qua tương quan chéo hoặc tích chập giữa **kernel** với ảnh hoặc ngược lại.

**Gợi ý: Độc giả không có nhu cầu hiểu mấu chốt các thuật toán có thể kéo thẳng xuống phần 3. Tóm tắt kiến thức**

## 2.1. Bộ lọc tương quan (Correlation filter)
Để áp dụng một bộ lọc vào ảnh, ta cần **kernel**. Kernel có thể được hiểu như lõi, là một ma trận mang tính chất đặc trưng quyết định đầu ra của ảnh thông qua thuật toán lọc ảnh. Ở bài tiếp theo, chúng ta sẽ bàn kỹ về tích chập ảnh với kernel và so sánh với tương quan chéo.

Giả sử một cửa sổ con có kích thước $k \\times k$ , với  $ k $  là số lẻ, ta tính được **tương quan (correlation)** dựa trên công thức:
$$
I[i,j] = \frac{1}{k^2}\sum_{u=-\frac{k-1}{2}}^{\frac{k-1}{2}}\sum_{v=-\frac{k-1}{2}}^{\frac{k-1}{2}}I_0[i+u,j+v]
\tag{1}
$$

Để thực hiện công thức (1), ta lần lượt cho $u$ và $v$ chạy từ *$-\frac{k-1}{2}$* đến *$\frac{k-1}{2}$* rồi cộng các kết quả của từng $I_0[i+u,j+v]$ lại. Tham số $\frac{1}{k^2}$ được gọi là **trọng số mặc định (uniform weights)**.

Bây giờ hãy thử thay các trọng số riêng rẻ bằng một ma trận chứa nhiều trọng số nào! Ta gọi ma trận đó là $K$.

$$
I[i,j] = \sum_{u=-\frac{k-1}{2}}^{\frac{k-1}{2}}\sum_{v=-\frac{k-1}{2}}^{\frac{k-1}{2}}K[u,v]I_0[i+u,j+v]
\tag{2}
$$

Công thức (2) được biết đến là **tương quan chéo (cross-correlation)**. Nếu xem $K[u,v]$ là một **kernel** (kernel vốn là ma trận chứa các trọng số) và $I_0[i+u,j+v]$ là ảnh gốc thì ta có ngay và luôn công thức tương quan chéo kernel với ảnh. Thuật toán này hoạt động rất đơn giản: cho lần lượt $u$, $v$ chạy từ *$-\frac{k-1}{2}$* đến *$\frac{k-1}{2}$*, ta nhân từng hệ số tại vị trí $[u,v]$ của ma trận $K$ với hệ số tại vị trí $[i+u,j+v]$ của ma trận ảnh gốc $I_0$ rồi cộng các kết quả lại với nhau. 

Trong thư viện Scipy có sẵn hàm [scipy.ndimage.correlate](https://docs.scipy.org/doc/scipy/reference/generated/scipy.ndimage.correlate.html#scipy.ndimage.correlate), bạn đọc có thể tham khảo đường link để áp dụng luôn sau này.

## 2.2. Nhiễu Gauss (Gaussian noise)
Trong thực tế có 3 loại nhiễu thường gặp nhất: **nhiễu đốm (speckle noise)**, **nhiễu muối tiêu (pepper-and-salt noise)** và **nhiễu Gauss (Gaussian noise)**. Nhiễu đốm xuất hiện khi các sóng tới (sóng ánh sáng, siêu âm, điện từ,...) giao thoa với nhau tạo nên các điểm cực đại hoặc cực tiểu. Nhiễu muối tiêu xuất hiện do sự gián đoạn việc truyền/nhận tín hiệu giữa nguồn phát và cảm biến. **Nhiễu Gauss**, được đặt theo tên nhà toán học người Đức **Gauß** (hoặc Gauss), là một loại nhiễu có **mật độ xác suất (probability density)** tương ứng với **phân phối chuẩn (normal distribution)**. Hay nói cách khác, biểu đồ mà nhiễu Gauss tạo nên cũng có hình dạng cái chuông.
<center><img src="VietCS Blog/Nhập môn Image filtering/Gaussian distribution.png" width=400> </center>
<center> Hình dạng cái chuông của phân phối Gauss (Gaussian distribution) </center>


Công thức mật độ xác suất Gauss:
$$
f(x|\mu, \sigma^2) = \frac{1}{\sigma\sqrt{2\pi}}e^{-\frac{(x-\mu)^2}{2\sigma^2}}
\tag{3}
$$
trong đó:

$\sigma$: độ lệch chuẩn (standard deviation)

$var = \sigma^2$: phương sai (variance)

$\mu$: số trung bình (mean value)

$x$: vị trí tọa độ

Số trung bình $\mu$ thường mặc định là 0. Vì thế tham số quyết định hình dạng của biểu đồ Gauss trong các thuật toán thường là độ lệch chuẩn $\sigma$ hoặc phương sai $var$. Suy rộng ra trong không gian 2D, hàm số Gauss là tích của 2 hàm số Gauss trên trục x và y, với $\mu = 0$ thì viết gọn sẽ là:
$$
f(x,y) = f(x)f(y) = \frac{1}{2\pi\sigma^2}e^{-\frac{x^2+y^2}{2\sigma^2}}
\tag{4}
$$
<center><img src='VietCS Blog/Nhập môn Image filtering/Gaussian 2D distribution.svg'width=400></center>
<center> Phân bố Gauss trong không gian 2D </center>

Gọi $I_{noisy}$, $I_{0}$, $A_{noise}$ lần lượt là ma trận ảnh bị nhiễu Gauss, ma trận ảnh gốc và ma trận nhiễu Gauss thì ta sẽ có công thức:
$$
I_{noisy}{[i,j]} = I_{0}{[i,j]} + A_{noise}{[i,j]}
\tag{5}
$$

Việc tạo nhiễu Gauss thường dùng để thử bộ lọc, vì thế đây là một thuật toán tuy đơn giản nhưng rất quan trọng sau này. Với tinh thần ứng dụng thực tiễn, ta dùng hẵn hàm [skimage.util.random_noise](https://scikit-image.org/docs/dev/api/skimage.util.html#skimage.util.random_noise) có sẵn luôn.


```python
# Nạp các thư viện cần thiết
import numpy as np
from matplotlib import pyplot as plt
from skimage.util import random_noise

image = plt.imread('Data/CT_Brain.jpg') # Đọc ảnh đầu vào
img = random_noise(image, var=0.05) # Hàm random_noise dùng để tạo nhiễu Gauss
plt.figure(figsize=(10,5)) # Tạo cửa sổ hiển thị
plt.subplot(121); plt.imshow(image, cmap='gray'); plt.title('Original') # Hiển thị ảnh gốc
plt.subplot(122); plt.imshow(img, cmap='gray'); plt.title('Noisy image') # Hiển thị ảnh bị nhiễu
```

<center><img src='VietCS Blog/Nhập môn Image filtering/Gaussian noise output.PNG' width=400></center>

<center> Kết quả thuật toán </center>

## 2.3. Bộ lọc trung bình (Mean filter)

**Bộ lọc trung bình (mean filter)** được xếp vào loại **bộ lọc tuyến tính (linear filter)**. Kernel của bộ lọc trung bình là một ma trận kích thước $k\times k$ ($k$ là số lẻ) với các phần tử bằng 1. Đầu ra của bộ lọc sẽ là giá trị trung bình của tổng các phần tử nằm trong kernel nơi nó đi qua. Hại não quá hả? Thôi giải thích bằng hình ảnh cho dễ hiểu nha!

<center><img src='VietCS Blog/Nhập môn Image filtering/Mean filter calculation.png' width=400></center>

Các ma trận ở hàng trên là ma trận ảnh gốc, hàng dưới là ảnh xuất ra và vùng xám là kernel. Ta lấy ví dụ hình bên trái. Theo quy tắc tương quan chéo kernel với ảnh ở (2) thì ta có:

$$
(1\times10)+(1\times41)+(1\times9)+(1\times232)+(1\times186)+(1\times79)+(1\times1)+(1\times43)+(1\times56)=657
$$
Sau đó ta chia trung bình giá trị này, tức chia 9:
$$
\frac{657}{9}=73
$$
Đầu ra của kernel là một giá trị nằm ở tâm kernel đó. Bạn hãy để ý vị trí của số 186 màu đỏ ở trên và 73 ở dưới. Cứ thế, ta cho kernel chạy qua tất cả vị trí trong ảnh thì ta sẽ được một ảnh lọc hoàn chỉnh.

Có bạn thắc mắc: vậy đối với giá trị 10, tức vị trí $[1,1]$ của ma trận ảnh thì tính toán thế nào? Quả thật tại giá trị 10, kernel bị "thừa" ra ngoài nên không thể áp dụng nhân tương quan. Để giải quyết việc này, ta sẽ **đệm (pad)** ma trận ảnh. Hình dưới ta gọi kỹ thuật này là **zero padding**, tức đệm số 0.

<center><img src='VietCS Blog/Nhập môn Image filtering/Zero padding.png' width=300></center>

Ảnh gốc và ảnh xuất chỉ có kích thước $4\times4$ thôi nhưng khi ta "đệm" số 0 vào các hàng và cột thì ta sẽ có ảnh gốc kích thước $6\times6$.  Thật ra còn nhiều kỹ thuật đệm khác như **đệm đối xứng (symmetric padding)**, **đệm phản chiếu (reflect padding)**, **đệm hằng số (constant padding)**, **đệm rìa (edge padding)** nhưng tui không tiện diễn giải vì chúng không tạo quá nhiều sự khác biệt với nhau khi xuất ảnh.

Bây giờ hãy thử viết thuật toán lọc ảnh trung bình từ đầu:


```python
# Nạp các thư viện cần thiết
import numpy as np
from matplotlib import pyplot as plt
from scipy import ndimage

# Đọc ảnh đầu vào và hiển thị
img = plt.imread('Data/HaThao_1.jpg') # Đọc ảnh
plt.figure(figsize=(10,5)) # Tạo cửa sổ hiển thị
plt.subplot(121), plt.imshow(img),plt.title('Original') # Hiển thị ảnh gốc

# Tạo hàm mean_filter 
def mean_filter(img,n):
    kernel = np.ones((n,n,1))/n**2 # Tạo ma trận kernel
    img_filt = ndimage.correlate(img,kernel) # Tương quan chéo kernel với ảnh
    return img_filt # Xuất ra ảnh đã lọc

img_filt = mean_filter(img,9) # Gọi hàm số mean_filter và thực hiện tính toán
plt.subplot(122), plt.imshow(img_filt),plt.title('Mean filter') # Hiển thị ảnh xuất
```

<center><img src='VietCS Blog/Nhập môn Image filtering/Mean filter output.PNG' width=400></center>

<center> Kết quả thuật toán </center>


Trong ảnh gốc, da mặt Hạ Thảo lấm tấm nhiều nốt mụn đỏ (mà thực ra do tui dùng Paint chấm lên) nhưng sau khi qua bộ lọc thì chúng đã mờ đi rất nhiều. Trong thực tế, các ứng dụng selfie và photoshop dùng bộ lọc trung bình dưới dạng vùng khoanh nhỏ thay vì áp dụng vào toàn bộ khuôn hình như trên.

## 2.4. Bộ lọc Gauss (Gaussian filter)
**Bộ lọc Gauss (Gaussian filter)** được dùng nhiều hơn bộ lọc trung bình vì những ưu điểm vượt trội của nó. Bộ lọc Gauss tuân thủ phân bố ... Gauss nhưng dưới thể rời rạc. Kernel kích thước $3\times3$ và $5\times5$ với một tham số $\sigma$ cụ thể sẽ nhìn giống thế này:

<center><img src='VietCS Blog/Nhập môn Image filtering/Gaussian kernel.PNG' width=300></center>



Bây giờ ta bắt tay vào viết code luôn. Thư viện scikit-image có hỗ trợ hàm [skimage.filters.gaussian](https://scikit-image.org/docs/dev/api/skimage.filters.html#skimage.filters.gaussian) để lọc Gauss.


```python
import numpy as np
from matplotlib import pyplot as plt
from skimage.filters import gaussian
import cv2 

img = plt.imread('Data/HaThao_1.jpg')
plt.figure(figsize=(10,5))
plt.subplot(121), plt.imshow(img), plt.title('Original')

img_filt = gaussian(img,sigma=2.2, multichannel=True)
#img_filt = cv2.GaussianBlur(img, ksize=(7,7),sigmaX=2.2) # OpenCV hỗ trợ tùy chọn kích thước kernel, Scikit-image thì không
plt.subplot(122), plt.imshow(img_filt), plt.title('Gaussian filter')
```

<center><img src='VietCS Blog/Nhập môn Image filtering/Gaussian filter output.PNG' width=400></center>

<center>Kết quả thuật toán</center>


So sánh với bộ lọc trung bình, các bạn hãy để ý thấy rằng phần tóc, chân mày và con ngươi của Hạ Thảo không bị "quá mờ" so với làn da và nền. Trong thị giác máy tính, viền (ví dụ biên giới giữa tóc và da) đóng vai trò quan trọng trong thuật toán nhận diện vật thể và tách ảnh. Vì vậy, việc loại bỏ nhiễu nhưng vẫn giữ lại viền được khuyến nghị. Đó cũng chính là ưu điểm của bộ lọc Gauss: **bảo tồn viền (edge preserving)**.

## 2.5. Bộ lọc trung vị (Median filter)
**Bộ lọc trung vị (median filter)** được xếp vào loại **bộ lọc phi tuyến tính (non-linear filter)**. Bộ lọc trung vị hoạt động giống như bộ lọc trung bình, nhưng thay vì xuất ra giá trị trung bình, nó xuất ra trung vị của các phần tử trong cửa sổ mà nó đi qua. Giả sử ta có một kernel kích thước $3\times3$:

<center><img src='VietCS Blog/Nhập môn Image filtering/Median filter how it works.gif' width=400></center>

Các phần tử trong kernel là: 124, 126, 127, 120, 150, 125, 115, 119, 123. Trong đó trung vị là: 124. Vậy tại vị trí tâm của kernel, tức vị trí giá trị 150 sẽ xuất ra trung vị 124.

Bộ lọc trung vị loại bỏ nhiễu đốm và nhiễu muối tiêu tốt hơn bộ lọc trung bình và Gauss. Ta hãy cùng thử một bài toán thực tế về xử lý ảnh y khoa nhé! Hàm tạo bộ lọc trung vị được dùng sẽ là [skimage.filters.median](https://scikit-image.org/docs/dev/api/skimage.filters.html#skimage.filters.median)


```python
from matplotlib import pyplot as plt
from skimage.color import rgb2gray
from skimage.util import random_noise
from skimage.filters import median
from skimage.morphology import rectangle

img = plt.imread('Data/CT_Brain.jpg')
img = rgb2gray(img)
plt.figure(figsize=(10,5))
plt.subplot(131), plt.imshow(img, cmap='gray'), plt.title('Original')

img_n = random_noise(img, var=0.01)
plt.subplot(132), plt.imshow(img_n, cmap='gray'), plt.title('Noisy image')
kernel = rectangle(5,5) # Tạo kích thước kernel 5x5
img_filt = median(img_n,selem=kernel)
plt.subplot(133), plt.imshow(img_filt, cmap='gray'), plt.title('Median filter')
```

<center><img src='VietCS Blog/Nhập môn Image filtering/Median filter output.PNG' width=600></center>

<center>Kết quả thuật toán</center>


## 2.6. So khớp mẫu bằng bộ lọc (template matching with filters)
Hãy tưởng tượng bạn muốn tìm người quen giữa rừng người mênh mông. **Template matching** sẽ giúp bạn trong trường hợp này.

Trước hết bạn phải tìm một bức ảnh chụp người ấy, gọi là **mẫu (template)**, để đối chiếu với ảnh gốc (tức ảnh chụp cả rừng người dưới đây. Ô màu xanh lá là ô xác định tìm thấy người quen bạn sau khi chạy thuật toán **so khớp mẫu**.

<center><img src='VietCS Blog/Nhập môn Image filtering/Template matching example.PNG' width=500></center>

Từ công thức (2), ta thay $K[u,v]$ bằng $T[u,v]$, viết tắt cho template và rút gọn ký hiệu $\sum$:
$$
I[i,j] = \sum_{u,v=-\frac{k-1}{2}}^{\frac{k-1}{2}}T[u,v]I_0[i+u,j+v]
\tag{6}
$$

Bây giờ ta chuẩn hóa phương trình (6):
$$
I[i,j] = \frac{\sum_{u,v=-\frac{k-1}{2}}^{\frac{k-1}{2}}T[u,v]I_0[i+u,j+v]}{\sqrt{\sum_{u,v=-\frac{k-1}{2}}^{\frac{k-1}{2}}T[u,v]^2
\sum_{u,v=-\frac{k-1}{2}}^{\frac{k-1}{2}}I_0[i+u,j+v]^2}}
\tag{7}
$$

Phương trình (7) được gọi là **tương quan chéo chuẩn hóa (normalized cross-correlation)**. Áp dụng (7) ta sẽ ra kết quả như sau:

<center><img src='VietCS Blog/Nhập môn Image filtering/Template matching result.PNG'width=500></center>

Nhìn vào ảnh đã được so khớp mẫu, bạn có thấy chấm màu trắng trong vùng khoanh tròn không? Đó là điểm có độ sáng (**intensity**) lớn nhất và cũng là tâm của mẫu được xác định. Phương trình (7) đạt giá trị lớn nhất chỉ khi giá trị các phần tử của mẫu đúng bằng cửa sổ con.

Bây giờ ta thực hành luôn nhé! Ta sẽ sử dụng hàm [skimage.feature.match_template](https://scikit-image.org/docs/dev/api/skimage.feature.html#skimage.feature.match_template) 


```python
import numpy as np
import matplotlib.pyplot as plt
from skimage import data
from skimage.feature import match_template

image = data.coins() # Nạp ảnh các đồng xu từ dữ liệu có sẵn của scikit-image
coin = image[170:220, 75:130] # Cắt ảnh một đồng xu ra để làm mẫu

result = match_template(image, coin) # Khâu so khớp mẫu
ij = np.unravel_index(np.argmax(result), result.shape) # Xác định vị trí chấm sáng nhất
x, y = ij[::-1]

# Hiển thị các ảnh và đánh dấu mẫu đã được khớp
fig = plt.figure(figsize=(10, 5))
ax1 = plt.subplot(1, 3, 1)
ax2 = plt.subplot(1, 3, 2)
ax3 = plt.subplot(1, 3, 3, sharex=ax2, sharey=ax2)

ax1.imshow(coin, cmap=plt.cm.gray)
ax1.set_axis_off()
ax1.set_title('template')

ax2.imshow(image, cmap=plt.cm.gray)
ax2.set_axis_off()
ax2.set_title('image')

hcoin, wcoin = coin.shape
rect = plt.Rectangle((x, y), wcoin, hcoin, edgecolor='r', facecolor='none')
ax2.add_patch(rect)

ax3.imshow(result)
ax3.set_axis_off()
ax3.set_title('`match_template`\nresult')

ax3.autoscale(False)
ax3.plot(x, y, 'o', markeredgecolor='r', markerfacecolor='none', markersize=10)

# In kết quả
print('Vị trí chấm sáng nhất là: (i,j) = (%d,%d)' %(x,y))
```


    Vị trí chấm sáng nhất là: (i,j) = (75,170)

<center><img src='VietCS Blog/Nhập môn Image filtering/Template matching output.PNG'width=400></center>

<center>Kết quả thuật toán</center>

Trong so khớp mẫu, tui phải nhắc đến 2 điều. Điều thứ nhất: ngoài phương trình **normalized cross-correlation (NCC)**, người ta còn dùng phương trình **tổng các hiệu số tuyệt đối (sum of absolute differences)**, vì e các bạn "tẩu hỏa nhập ma" nên tui sẽ đề cập đến khi VietCS đủ lớn. Điều thứ hai: trong các bài toán thực tế, bạn sẽ không có một mẫu giống y chang trong ảnh gốc. Mẫu bạn có sẽ như thế này:

<center><img src='VietCS Blog/Nhập môn Image filtering/Non identical template matching.PNG'width=350></center>

Rõ ràng vật thể trong mẫu và ảnh gốc khác nhau mặc dù chúng đều là xe. Vì thế thuật toán so khớp mẫu cần phải cải tiến thêm, cụ thể là phải ứng dụng các **mạng thần kinh tích chập (convolution neural network)**. Ta cũng sẽ bàn đến cái này sau.

## 3. Tóm tắt kiến thức
<u> 1) Bộ lọc tương quan (Correlation filter) </u>

Phương trình tương quan chéo giữa ảnh và kernel:
$$
I[i,j] = \sum_{u=-\frac{k-1}{2}}^{\frac{k-1}{2}}\sum_{v=-\frac{k-1}{2}}^{\frac{k-1}{2}}K[u,v]I_0[i+u,j+v]
\tag{2}
$$

trong đó:

$I[i,j]$: ma trận ảnh đầu ra

$I_0[i,j]$: ma trận ảnh gốc

$K[u,v]$: ma trận kernel

$k$: kích thước kernel

Hàm tính tương quan chéo ảnh với kernel: [scipy.ndimage.correlate](https://docs.scipy.org/doc/scipy/reference/generated/scipy.ndimage.correlate.html#scipy.ndimage.correlate) 

<u> 2) Nhiễu Gauss (Gaussian noise) </u>

Hàm số Gauss trong không gian 2D:
$$
f(x,y) = f(x)f(y) = \frac{1}{2\pi\sigma^2}e^{-\frac{x^2+y^2}{2\sigma^2}}
\tag{4}
$$

Hàm tạo nhiễu Gauss: [skimage.util.random_noise](https://scikit-image.org/docs/dev/api/skimage.util.html#skimage.util.random_noise) 

<u> 3) Bộ lọc trung bình (Mean filter) </u>

 [skimage.filters.rank.mean](https://scikit-image.org/docs/dev/api/skimage.filters.rank.html#skimage.filters.rank.mean) 

<u> 4) Bộ lọc Gauss (Gaussian filter) </u>

Hàm áp bộ lọc Gauss vào ảnh: [skimage.filters.gaussian](https://scikit-image.org/docs/dev/api/skimage.filters.html#skimage.filters.gaussian) 

<u> 5) Bộ lọc trung vị (Median filter) </u>

Hàm áp bộ lọc trung vị vào ảnh: [skimage.filters.median](https://scikit-image.org/docs/dev/api/skimage.filters.html#skimage.filters.median) 

<u> 6) So khớp mẫu (Template matching) </u>

Hàm so ảnh mẫu xem khớp với ảnh gốc tại vị trí nào: [skimage.feature.match_template](https://scikit-image.org/docs/dev/api/skimage.feature.html#skimage.feature.match_template) 

<u> 7) Phân loại bộ lọc theo ứng dụng: </u>

Tăng cường ảnh (**Image enhancement**): bộ lọc trung bình, trung vị, Gauss,...

Nhận diện viền (**Edge detection**): bộ lọc Sobel, Prewitt, Roberts cross, Deriche,...

Tách ảnh (**Image segmentation**): bộ lọc Gabor, Log Gabor,...

Nhận diện điểm nhỏ (**Blob detection**): bộ lọc Laplacian of Gaussian (LoG), ...

## 4. Bài tập

* Hãy thử viết chương trình "bộ lọc trung bình" bằng Numpy
* Hãy thử tạo ma trận nhiễu Gauss trong không gian 2D bằng Numpy
* Hãy thử viết chương trình "so khớp mẫu" bằng Numpy

**Gợi ý: Độc giả có thể tìm code trên mạng, miễn là bạn hiểu nguyên lý hoạt động của nó**



## Nguồn tham khảo:

Nguồn ảnh:

1. [Wikipedia](https://vi.wikipedia.org/wiki/Trang_Ch%C3%ADnh)
2. [Wiki Uni Göttingen](http://wiki.awf.forst.uni-goettingen.de/)
3. [Stackoverflow](https://stackoverflow.com/)
4. [XRDS: The ACM Magazine for Students]( https://blog.xrds.acm.org/ )
5. [University of Edinburg: School of Informatics]( https://homepages.inf.ed.ac.uk/rbf/HIPR2/median.htm)
6. [Deeplizard](https://deeplizard.com/)
7. [Udacity](https://udacity.com/)

Nguồn tài liệu:

1. [Wiki: Kernel (image processing)]( https://en.wikipedia.org/wiki/Kernel_(image_processing) )
2. [Udacity: Introduction to Computer Vision]( https://classroom.udacity.com/courses/ud810 )
3. [Wiki: Gaussian distribution]( https://en.wikipedia.org/wiki/Normal_distribution )
4. [OpenCV: Normalized cross-correlation]( https://docs.opencv.org/2.4/modules/imgproc/doc/object_detection.html?highlight=matchtemplate#matchtemplate)
5. [Scikit-image: Template matching example codes]( https://scikit-image.org/docs/dev/auto_examples/features_detection/plot_template.html#sphx-glr-auto-examples-features-detection-plot-template-py )



